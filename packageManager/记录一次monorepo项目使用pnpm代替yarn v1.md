# 现状

项目基于yarn workspace进行Monorepo工程的包依赖管理，目前会存在以下几个问题：

> 注：项目之前是node 12版本，长期使用yarn的v1版本，下面问题皆以v1版本进行描述：

* 包安装机制消耗：每个项目进行包安装时，包从网络下载或从缓存目录拷贝至项目目录中。因此，当不同项目目录中有相同依赖时，也会进行相同的安装动作，每个项目都会产生有一个庞大的包体积占用，并且即使安装过程的依赖包是从缓存目录拷贝，也有一定的磁盘I/O性能和时间消耗。
* 链接本地包不稳定（yarn link）：yarn会在项目的根目录及各个workspace的package目录下自动链接本地的包，但此链接动作需要项目指定的依赖版本与本地包的版本号一致，如果版本号不一致，则项目会从远程下载对应版本的包，此时不再链接本地包中代码的修改。
* 依赖提升与幽灵依赖问题：

  > yarn有依赖提升行为，该行为始于`npm@2`​升级至`npm@3`​时将`node_modules`​从纵深结构变成扁平结构，yarn也使用了该扁平结构，详情如下：
  >
  > * ​`npm@2`​-纵深结构：`node_modules`​中的每个依赖项都有其自己的`node_modules`​文件夹，其所有依赖项都在`package.json`​中指定。此时`node_modules`​的结构是干净且可预测的，但相同依赖的包会重复下载，包体积占用大，包目录嵌套深，性能损耗大等问题
  > * ​`npm@3`​-扁平结构：按照`package.json`​里依赖的顺序依次解析，遇到新的包就把它放在第一级目录，后面如果遇到一级目录已经存在的包，会先判断版本，如果版本一样则忽略，否则会按照`npm@2`​的方式依次挂在依赖包目录下。此时`node_modules`​的结构是不干净的，会出现幽灵依赖。
  > * 幽灵依赖：一个库引用了其`package.json`​里没有指定依赖的包的情况，此现象会被Monorepo架构的依赖提升问题放大。
  >
* peerDependencies管理问题：yarn v1和[npm@3-6版本](https://docs.npmjs.com/cli/v10/using-npm/config#legacy-peer-deps)不会自动安装包指定的peerDependecies依赖，这可能会出现peerDependencies依赖缺失或版本不匹配问题。

# 包管理工具调研

> [https://yarnpkg.com/benchmarks](https://yarnpkg.com/benchmarks)

​![](/upload/image-jlcf.png)我们基于CI场景下的性能表现进行技术选型，预估升级yarn版本或替换为pnpm的提升效果，由上图初步判断pnpm相比yarn性能更优。

同时在兼容性方面，yarn v3有[三种运行模式](https://yarnpkg.com/features/linkers)，第一种是pnp模式，此模式升级更为激进，直接移除了node_modules文件夹，而第二和第三种分别是pnpm模式和yarn v1的安装模式，相较于pnpm方案与项目当前的yarn v1方案，直接忽略这两种模式。

最后，考虑升级yarn v3 pnp模式的改动量和不可预测性较大，优先采用pnpm方案进行升级查看效果。

# pnpm方案

基于上面的问题进行对比：

* 包安装机制：包唯一安装于缓存目录，所有项目中依赖的包会用硬链接形式关联缓存包，无拷贝消耗。
* 链接本地包：本地包存在时，pnpm会默认保持链接本地包。
* 依赖提升与幽灵依赖问题：pnpm默认不会依赖提升，不存在幽灵依赖问题。node_modules依然使用npm@2的纵深结构，由于包全部硬链接自缓存目录，所以不会存在包重复下载，体积占用大的问题。

  > 注：
  >
  > 当项目依赖提升导致幽灵依赖问题已经根深蒂固，难以化解时，项目想保留依赖提升机制，采用下面操作处理：
  >
  > 1. 在.npmrc中设置 shamefully-hoist = true 强制进行依赖提升。  
  >     其他设置项参考：
  >
  >     * [https://pnpm.io/zh/npmrc#public-hoist-pattern](https://pnpm.io/zh/npmrc#public-hoist-pattern)
  >     * [https://pnpm.io/zh/npmrc#node-linker](https://pnpm.io/zh/npmrc#node-linker)
  > 2. 由于yarn v1的依赖提升在monorepo工程中还会自动将本地包一起提升到项目的根目录node_modules中，如果你的项目工程基于根目录node_modules的本地包的引用，此时你需要在项目根目录下使用pnpm link手动关联本地包或在package.json指明workspace依赖。  
  >     参考：[https://github.com/pnpm/pnpm/issues/1585](https://github.com/pnpm/pnpm/issues/1585)
  > 3. 当依赖提升导致版本冲突时，可以考虑使用package.json的resolutions字段强制限定依赖版本。
  >
* peerDependencies管理问题：pnpm默认自动安装peerDependencies。

  > 注：
  >
  > 如果你的项目之前一直忽略peerDependencies的安装且没有出现问题，若想继续保持可采用下面操作处理：
  >
  > * 在.npmrc中设置auto-install-peers=false禁用peerDependencies自动安装。
  >

# Webpack5 学习笔记（2）-- 基础用法

> 本文我们开始正式介绍webpack的基本使用，除了前半部分的基础概念解释，后面的资源解析，文件监听及热更新，代码压缩，文件指纹部分会以代码案例形式进行介绍。

# **安装**

```shell
npm install webpack webpack-cli --save-dev
```

# **运行方式**

* CLI运行：webpack --config {指定配置文件}

  > 默认使用 webpack.config.js
  >
* 代码行运行：

  ```javascript
  var webpack = require('webpack') 
  var config = require('./webpack.config')
  webpack(config, (err, stats) => {})
  ```

> 推荐两种quick start方式：
>
> * 借助webpack-cli init命令，初始化项目中的webpack配置。
> * 借助webpack-cli create命令，创建一个webpack项目。

# 基本概念

下面是一些webpack的常用词汇，我们先做下简单了解：

* **Module**：Module是Webpack的基本构建单元，用来封装特定功能或组件，一个模块通常对应一个或多个相关联的资源文件（如：JS，CSS，HTML，图片等）。
* **Chunk**：Chunk是Webpack在构建过程中产生的代码块，它可以由一个或多个Module组成。Chunk可以由entry入口或异步加载模块或代码分割产生。
* **Bundle**：Bundle是Webpack最终输出的文件，通常认为Chunk在构建完成就呈现为bundle。

> Entry，Loader，Plugin等概念在下面配置说明中介绍。

# **配置说明**

通常，我们会在Webpack的配置文件中指定以下内容：

* ​`entry`​：指定文件的打包入口
* ​`output`​：指定编译后文件输出到磁盘的设置
* ​`mode`​：指定当前的构建环境：production，development，none，自动触发webpack内置函数。
* ​`loaders`​：在`module.rules`​规则中指定，通常的作用将其他类型的文件转化成有效模块，并添加到依赖图中。
* ​`plugins`​：用于bundle文件的优化，资源管理和环境变量注入，并作用于整个构建过程。

```javascript
module.exports = {
    entry:'./src/index.js',
    output:'./dist/main.js',
    mode:'production',
    module:{
        rules:[
            {
                test:/\.txt$/,
                use:'raw-loader'
            }
        ]
    },
    plugins:[
        new HtmlwebpackPlugin({
            template:'./src/index.html'
        })
    ]
};
```

## **配置 entry**

* 默认值：'./src/index.js'
* 使用场景：

  1. 单入口：适用于单页应用, entry 是一个字符串

      ```javascript
      module.exports = {
        entry: "./src/index.js",
      };
      ```
  2. 多入口：适用于多页应用, entry 是一个对象

      ```javascript
      module.exports = {
        entry: {
          app: "./src/app.js",
          adminApp: "./src/adminApp.js",
        },
      };
      ```

## **配置 output**

* 使用场景：

  * 单入口配置：

    ```javascript
    module.exports = {
      entry: "./src/index.js",
      output: {
        filename: "bundle",
        path: __dirname + "/dist",
      },
    };
    ```

    > 示例：[https://github.com/zephyr-133/webpack-demo/tree/master/01-single_entry](https://github.com/zephyr-133/webpack-demo/tree/master/01-single_entry)
    >
  * 多入口配置：通过占位符确保文件名称的唯一

    ```javascript
    module.exports = {
      entry: {
        app: "./src/app.js",
        adminApp: "./src/adminApp.js",
      },
      output:{
        filename:'[name].js',
        path:__dirname + '/dist'
      }
    };
    ```

    > 示例：[https://github.com/zephyr-133/webpack-demo/tree/master/02-multi_entry](https://github.com/zephyr-133/webpack-demo/tree/master/02-multi_entry)
    >

## **配置 loaders**

Webpack默认只支持JS和Json两种文件类型，需借助Loaders来处理其他文件类型，其本身是一个函数，接收源文件作为参数，并返回转换的结果。

* |常见Loaders：||
  |名称|描述|
  | ---------------| ------------------------------------------------------------|
  |babel-loader|转换ES6、ES7等JS新特性语法|
  |css-loader|支持.css文件的加载和解析，加载.css文件并转换成commonjs对象|
  |style-loader|将样式通过<style>标签插入到head中|
  |less-loader|将less文件转换成css|
  |ts-loader|将TS转换成JS|
  |file-loader|进行图片、字体等文件的打包|
  |raw-loader|将文件以字符串的形式导入|
  |thread-loader|多进程打包JS和CSS|
* 用法：

  1. **test ** 指定匹配规则
  2. **use ** 指定使用的loader名称，**值为数组时，多个loader串行执行，顺序从下到上依次执行**

  ```javascript
  const path = require('path');

  module.exports = {
    output:{
      filename:'bundle.js'
    },
    module:{
      rules:[
        {test:/\.txt$/,use:'raw-loader'}
      ]
    }
  }
  ```

## **配置 plugins**

​`plugins`​ 选项用于以各种方式自定义 webpack 构建过程。

* |常见plugins：||
  |名称|描述|
  | --------------------------| --------------------------------------------|
  |CommonsChunkPlugin|将chunks相同的模块代码提取成公共js|
  |CleanWebpackPlugin|清理构建目录|
  |ExtractTextWebpackPlugin|将CSS从bundle文件里提取成一个独立的CSS文件|
  |CopyWebpackPlugin|将文件或文件夹拷贝到构建的输出目录|
  |HtmlWebpackPlugin|创建html文件去承载输出的bundle|
  |UglifyjsWebpackPlugin|压缩JS|
  |ZipWebpackPlugin|将打包出的资源生成一个zip包|
* 用法：  
  插件放到`plugins`​数组中，插件执行顺序从上到下

  ```javascript
  const path = require('path');

  module.exports = {
    output:{
      filename:'bundle.js'
    },
    plugins:[
        new HtmlWebpackPlugin({template:'./src/index.html'})
    ]
  }
  ```

## **配置 mode**

* 默认值：‘production’
* |函数启用项列表：||
  |选项|描述|
  | ------------------| -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
  |development|* 设置process.env.NODE_ENV的值为development* 开启下面插件：1. NamedChunksPlugin：热更新阶段打印更新的Chunks名1. NamedModulesPlugin：热更新阶段打印更新的Module名|
  |production|* 设置process.env.NODE_ENV的值为production* 开启下面插件：1. FlagDependencyUsagePlugin，1. FlagIncludedChunksPlugin，1. ModuleConcatenationPlugin，1. NoEmitOnErrorsPlugin，1. OccurrenceOrderPlugin，1. SideEffectsFlagPlugin1. TerserPlugin|
  |none|不开启任何优化项|

## **参考手册**

```javascript
const path = require('path');

module.exports = {
  mode: "production", // "production" | "development" | "none"
  // Chosen mode tells webpack to use its built-in optimizations accordingly.
  entry: "./app/entry", // string | object | array
  // 默认为 './src'
  // 这里应用程序开始执行
  // webpack 开始打包
  output: {
    // webpack 如何输出结果的相关选项
    path: path.resolve(__dirname, "dist"), // string
    // 所有输出文件的目标路径
    // 必须是绝对路径（使用 Node.js 的 path 模块）
    filename: "bundle.js", // string
    // 「入口分块(entry chunk)」的文件名模板
    publicPath: "/assets/", // string
    // 输出解析文件的目录，url 相对于 HTML 页面
    library: "MyLibrary", // string,
    // 导出库(exported library)的名称
    libraryTarget: "umd", // 通用模块定义
    // 导出库(exported library)的类型
    /* 高级输出配置（点击显示） */
  },
  module: {
    // 关于模块配置
    rules: [
      // 模块规则（配置 loader、解析器等选项）
      {
        test: /\.jsx?$/,
        include: [
          path.resolve(__dirname, "app")
        ],
        exclude: [
          path.resolve(__dirname, "app/demo-files")
        ],
        // 这里是匹配条件，每个选项都接收一个正则表达式或字符串
        // test 和 include 具有相同的作用，都是必须匹配选项
        // exclude 是必不匹配选项（优先于 test 和 include）
        // 最佳实践：
        // - 只在 test 和 文件名匹配 中使用正则表达式
        // - 在 include 和 exclude 中使用绝对路径数组
        // - 尽量避免 exclude，更倾向于使用 include
        issuer: { test, include, exclude },
        // issuer 条件（导入源）
        enforce: "pre",
        enforce: "post",
        // 标识应用这些规则，即使规则覆盖（高级选项）
        loader: "babel-loader",
        // 应该应用的 loader，它相对上下文解析
        // 为了更清晰，`-loader` 后缀在 webpack 2 中不再是可选的
        // 查看 webpack 1 升级指南。
        options: {
          presets: ["es2015"]
        },
        // loader 的可选项
      },
      {
        test: /\.html$/,
        use: [
          // 应用多个 loader 和选项
          "htmllint-loader",
          {
            loader: "html-loader",
            options: {
              /* ... */
            }
          }
        ]
      },
      { oneOf: [ /* rules */ ] },
      // 只使用这些嵌套规则之一
      { rules: [ /* rules */ ] },
      // 使用所有这些嵌套规则（合并可用条件）
      { resource: { and: [ /* 条件 */ ] } },
      // 仅当所有条件都匹配时才匹配
      { resource: { or: [ /* 条件 */ ] } },
      { resource: [ /* 条件 */ ] },
      // 任意条件匹配时匹配（默认为数组）
      { resource: { not: /* 条件 */ } }
      // 条件不匹配时匹配
    ],
    /* 高级模块配置（点击展示） */
  },
  resolve: {
    // 解析模块请求的选项
    // （不适用于对 loader 解析）
    modules: [
      "node_modules",
      path.resolve(__dirname, "app")
    ],
    // 用于查找模块的目录
    extensions: [".js", ".json", ".jsx", ".css"],
    // 使用的扩展名
    alias: {
      // 模块别名列表
      "module": "new-module",
      // 起别名："module" -> "new-module" 和 "module/path/file" -> "new-module/path/file"
      "only-module$": "new-module",
      // 起别名 "only-module" -> "new-module"，但不匹配 "only-module/path/file" -> "new-module/path/file"
      "module": path.resolve(__dirname, "app/third/module.js"),
      // 起别名 "module" -> "./app/third/module.js" 和 "module/file" 会导致错误
      // 模块别名相对于当前上下文导入
    },
    /* 可供选择的别名语法（点击展示） */
    /* 高级解析选项（点击展示） */
  },
  performance: {
    hints: "warning", // 枚举
    maxAssetSize: 200000, // 整数类型（以字节为单位）
    maxEntrypointSize: 400000, // 整数类型（以字节为单位）
    assetFilter: function(assetFilename) {
      // 提供资源文件名的断言函数
      return assetFilename.endsWith('.css') || assetFilename.endsWith('.js');
    }
  },
  devtool: "source-map", // enum
  // 通过在浏览器调试工具(browser devtools)中添加元信息(meta info)增强调试
  // 牺牲了构建速度的 `source-map' 是最详细的。
  context: __dirname, // string（绝对路径！）
  // webpack 的主目录
  // entry 和 module.rules.loader 选项
  // 相对于此目录解析
  target: "web", // 枚举
  // bundle 应该运行的环境
  // 更改 块加载行为(chunk loading behavior) 和 可用模块(available module)
  externals: ["react", /^@angular\//],
  // 不要遵循/打包这些模块，而是在运行时从环境中请求他们
  serve: { //object
    port: 1337,
    content: './dist',
    // ...
  },
  // 为 webpack-serve 提供选项
  stats: "errors-only",
  // 精确控制要显示的 bundle 信息
  devServer: {
    proxy: { // proxy URLs to backend development server
      '/api': 'http://localhost:3000'
    },
    contentBase: path.join(__dirname, 'public'), // boolean | string | array, static file location
    compress: true, // enable gzip compression
    historyApiFallback: true, // true for index.html upon 404, object for multiple paths
    hot: true, // hot module replacement. Depends on HotModuleReplacementPlugin
    https: false, // true for self-signed, object for cert authority
    noInfo: true, // only errors & warns on hot reload
    // ...
  },
  plugins: [
    // ...
  ],
  // 附加插件列表
  /* 高级配置（点击展示） */
}
```

# 资源解析

下面我们用一些简单的代码案例帮助大家处理常用的资源解析场景，了解webpack的基本配置使用及一些常见注意事项。

> 代码案例基于Webpack5运行，与webpack4使用中的差异部分会各案例中指出。

## **解析ES6**

* 案例地址：[https://github.com/zephyr-133/webpack-demo/tree/master/03-parse_es6](https://github.com/zephyr-133/webpack-demo/tree/master/03-parse_es6)
* 开发过程：

  1. 安装

      ```shell
      npm install @babel/core @babel/preset-env babel-loader --save-dev
      ```
  2. webpack中添加babel-loader, 指定处理js文件

      ```javascript
      const path = require('path');

      module.exports = {
          entry:'./src/index.js',
          output: {
          filename: "bundle",
          path: path.resolve(__dirname,'dist'),
          },
          mode:'production',
          module:{
              rules:[
                  {
                      test:/\.js$/,
                      use:'babel-loader'
                  }
              }
          ]
      };
      ```
  3. 编辑babel-loader的配置文件: .babelrc

      ```javascript
      {
        "presets": [
          //解析ES6
          "@babel/preset-env",
        ], //一系列plugins的集合
        "plugins": [] //一个plugins对应一个功能
      }
      ```

## **解析JSX**

* 案例地址：[https://github.com/zephyr-133/webpack-demo/tree/master/04-parse_jsx](https://github.com/zephyr-133/webpack-demo/tree/master/04-parse_jsx)
* 开发过程：

  1. 安装

      ```shell
      npm install react react-dom  @babel/preset-react --save-dev
      ```
  2. webpack中添加babel-loader, 指定处理js文件

      ```javascript
      const path = require('path');

      module.exports = {
          entry: './src/index.js',
          output: {
              filename: "bundle.js",
              path: path.join(__dirname, 'dist'),
          },
          mode: 'production',
          module: {
              rules: [
                  {
                      test: /\.js$/,
                      use: 'babel-loader'
                  }
              ]
          }
      };
      ```
  3. 编辑babel-loader的配置文件: .babelrc

      ```javascript
      {
        "presets": [
          //解析ES6
          "@babel/preset-env",
          //解析React
          "@babel/preset-react"
        ], //一系列plugins的集合
        "plugins": [] //一个plugins对应一个功能
      }
      ```

## **解析CSS**

* 案例地址：[https://github.com/zephyr-133/webpack-demo/tree/master/05-parse_css](https://github.com/zephyr-133/webpack-demo/tree/master/05-parse_css)
* 开发过程：

  1. 安装

      ```shell
      npm install css-loader style-loader --save-dev
      ```
  2. webpack中添加css-loader，style-loader处理css文件，将样式通过<style>标签插入到head中

      > 模块 loader 可以链式调用。链中的每个 loader 都将对资源进行转换。链会逆序执行。第一个 loader 将其结果（被转换后的资源）传递给下一个 loader，依此类推。最后，webpack 期望链中的最后的 loader 返回 JavaScript。
      >
      > 应保证 loader 的先后顺序：`'style-loader'`​ 在前，而 `'css-loader'`​ 在后。如果不遵守此约定，webpack 可能会抛出错误。
      >

      ```javascript
      const path = require('path');

      module.exports = {
          entry: './src/index.js',
          output: {
              filename: "bundle.js",
              path: path.join(__dirname, 'dist'),
          },
          mode: 'production',
          module: {
              rules: [
                  {
                      test: /\.js$/,
                      use: 'babel-loader'
                  },
                  {
                      test: /\.css$/,
                      use: [
                          'style-loader',
                          'css-loader'
                      ]
                  }
              ]
          }
      };
      ```

## **解析Less**

* 案例地址：[https://github.com/zephyr-133/webpack-demo/tree/master/06-parse_less](https://github.com/zephyr-133/webpack-demo/tree/master/06-parse_less)
* 开发过程：

  1. 安装

      ```shell
      npm install less less-loader --save-dev
      ```
  2. webpack中添加less-loader处理less文件，将less转换从css

      ```javascript
      const path = require('path');

      module.exports = {
          entry: './src/index.js',
          output: {
              filename: "bundle.js",
              path: path.join(__dirname, 'dist'),
          },
          mode: 'production',
          module: {
              rules: [
                  {
                      test: /\.js$/,
                      use: 'babel-loader'
                  },
                  {
                      test: /\.less$/,
                      use: [
                          'style-loader',
                          'css-loader',
                          'less-loader'
                      ]
                  }
              ]
          }
      };
      ```

## **解析图片和字体**

> 在 webpack 5 之前，通常使用：
>
> * ​`raw-loader`​ 将文件导入为字符串
> * ​`url-loader`​ 将文件作为 data URI 内联到 bundle 中
> * ​`file-loader`​ 将文件发送到输出目录
>
> 而在webpack 5中新增了资源模块类型(asset module type)，通过添加 4 种新的模块类型，来替换所有这些 loader：
>
> * ​`asset/resource`​ 发送一个单独的文件并导出 URL。之前通过使用 `file-loader`​ 实现。
> * ​`asset/inline`​ 导出一个资源的 data URI。之前通过使用 `url-loader`​ 实现。
> * ​`asset/source`​ 导出资源的源代码。之前通过使用 `raw-loader`​ 实现。
> * ​`asset`​ 在导出一个 data URI 和发送一个单独的文件之间自动选择。之前通过使用 `url-loader`​，并且配置资源体积限制实现。
>
> 如果我们还想要继续使用旧的assets loader（如 file-loader、url-loader、raw-loader），我们通过将asset模块设置为‘javascript/auto’来解决。

### 使用旧的assets loader

* 案例地址：[https://github.com/zephyr-133/webpack-demo/tree/master/07-parse_image_fonts_use_loader](https://github.com/zephyr-133/webpack-demo/tree/master/07-parse_image_fonts_use_loader)
* 开发过程：

  1. 安装

      ```shell
      npm install file-loader --save-dev
      ```
  2. webpack中添加file-loader处理image,font文件

      ```javascript
      const path = require('path');

      module.exports = {
          entry: './src/index.js',
          output: {
              filename: "bundle.js",
              path: path.join(__dirname, 'dist'),
          },
          mode: 'production',
          module: {
              rules: [
                  {
                      test: /\.js$/,
                      use: 'babel-loader'
                  },
                  {
                      test: /\.less$/,
                      use: [
                          'style-loader',
                          'css-loader',
                          'less-loader'
                      ]
                  },
                  {
                      test: /\.(png|jpg|gif|jpeg)$/,
                      type: 'javascript/auto',
                      use: {
                          loader: 'file-loader',
                          options:{
                              name: '[name].[ext]',
                              outputPath:"assets/images/",
                              publicPath:"./assets/images/",
                              esModule: false,
                          }
                      },
                  },
                  {
                      test: /\.(eot|ttf|otf|woff(2)?)(\?[a-z0-9]+)?$/,
                      type: 'javascript/auto',
                      use: {
                          loader: 'file-loader',
                          options:{
                              name: '[name].[ext]',
                              outputPath:"assets/fonts/",
                              publicPath:"./assets/fonts/",
                              esModule: false,
                          }
                      },
                  }
              ]
          }
      };
      ```

### 使用webpack5 assets module type

* 案例地址：[https://github.com/zephyr-133/webpack-demo/tree/master/08-parse_image_fonts_use_assetstype](https://github.com/zephyr-133/webpack-demo/tree/master/08-parse_image_fonts_use_assetstype)
* 开发过程：

  1. webpack中添加“asset/resource”处理image,font文件

      ```javascript
      const path = require('path');

      module.exports = {
          entry: './src/index.js',
          output: {
              filename: "bundle.js",
              path: path.join(__dirname, 'dist'),
          },
          mode: 'production',
          module: {
              rules: [
                  {
                      test: /\.js$/,
                      use: 'babel-loader'
                  },
                  {
                      test: /\.less$/,
                      use: [
                          'style-loader',
                          'css-loader',
                          'less-loader'
                      ]
                  },
                  {
                      test: /\.(png|jpg|gif|jpeg)$/,
                      type: 'asset/resource',
                      generator: {
                          filename: './assets/images/[name][ext]',
                      },
                  },
                  {
                      test: /\.(eot|ttf|otf|woff(2)?)(\?[a-z0-9]+)?$/,
                      type: 'asset/resource',
                      generator: {
                          filename: './assets/fonts/[name][ext]',
                      },
                  }
              ]
          }
      };
      ```

# 文件监听及热更新

在项目每次编译代码时，手动运行 `npm run build`​ 会显得很麻烦。

Webpack 为我们提供几种可选方式，帮助在代码发生变化后自动编译代码：

1. [webpack&apos;s Watch Mode](https://webpack.docschina.org/configuration/watch/#watch)
2. [webpack-dev-server](https://github.com/webpack/webpack-dev-server)
3. [webpack-dev-middleware](https://github.com/webpack/webpack-dev-middleware)

在大多数场景中，我们会使用 `webpack-dev-server`​，下面我们通过代码案例了解一下以上三种方式的使用。同时，Webpack在自动编译的基础上为我们提供了模块热替换（HMR）的功能，这个功能也在下面一并介绍。

## 使用watch mode（观察模式）

指示webpack监听依赖图中所有文件的更改，当某个文件被更新时，代码会自动重新构建出新的输出文件。

* 案例地址：[https://github.com/zephyr-133/webpack-demo/tree/master/09-webpack_watch](https://github.com/zephyr-133/webpack-demo/tree/master/09-webpack_watch)
* 开发过程（两种方式）：

  * 方法1：启用webpack命令时，添加watch参数，修改package.json如下：

    ```json
    {
      "name": "webpack-demo",
      "version": "1.0.0",
      "description": "",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1",
        "build": "webpack",
    +    "watch": "webpack --watch"
      },
      "author": "",
      "license": "ISC",
      "devDependencies": {
        "@babel/core": "^7.22.11",
        "@babel/preset-env": "^7.22.14",
        "@babel/preset-react": "^7.22.5",
        "babel-loader": "^9.1.3",
        "css-loader": "^6.8.1",
        "less-loader": "^11.1.3",
        "react": "^18.2.0",
        "react-dom": "^18.2.0",
        "style-loader": "^3.3.3",
        "webpack": "^5.88.2",
        "webpack-cli": "^5.1.4",
        "webpack-dev-server": "^4.15.1"
      }
    }
    ```
  * 方法2：在webpack.config.js 中设置watch：true

    ```javascript
    const path = require('path');

    module.exports = {
        entry: './src/index.js',
        output: {
            filename: "bundle.js",
            path: path.join(__dirname, 'dist'),
        },
        //默认false，不开启
        watch: true,
        //只有开启监听模式，wachOptions才生效
        watchOptions: {
            //默认为空，此选项指定了哪些文件或文件夹不应该被监听。
            ignored: /node_modules/,
            //这个选项定义了文件变化后，Webpack在重新构建之前等待的时间（以毫秒为单位）。在这里，Webpack将等待300毫秒，以允许多个文件变化在一起时一次性重新构建，以减少构建次数，提高效率。
            aggregateTimeout: 300,
            //如果系统不支持文件系统事件监听，Webpack会使用轮询（polling）的方式检查文件是否发生变化.
            //这个选项定义了轮询的时间间隔，即每隔1000毫秒（1秒）Webpack会检查一次文件变化
            poll: 1000
        },
        mode: 'development',
        module: {
            rules: [
                {
                    test: /\.js$/,
                    use: 'babel-loader'
                },
                {
                    test: /\.less$/,
                    use: [
                        'style-loader',
                        'css-loader',
                        'less-loader'
                    ]
                },
                {
                    test: /\.(png|jpg|gif|jpeg)$/,
                    type: 'asset/resource',
                    generator: {
                        filename: './assets/images/[name][ext]',
                    },
                },
                {
                    test: /\.(eot|ttf|otf|woff(2)?)(\?[a-z0-9]+)?$/,
                    type: 'asset/resource',
                    generator: {
                        filename: './assets/fonts/[name][ext]',
                    },
                }
            ]
        }
    };
    ```
* 缺陷：为了看到修改后的实际效果，每次需要手动刷新浏览器。

  > 如果你使用VS Code编辑器并安装了Live Server插件，使用该插件在浏览器查看输出文件内容，则Live Server会在你的项目文件夹启动一个本地服务器，实时预览你的HTML，CSS和JavaScript文件，它会自动检测HTML等文件的更改，并在保存文件时，自动刷新浏览器，以便你可以立即查看更改，可以规避这个缺陷。
  >

## 使用webpack-dev-server

webpack-dev-server为你提供了一个基本的web server，并且具有live reloading（实时重新加载）功能。修改文件时，告知dev server，从什么位置查找文件。

* 案例地址：[https://github.com/zephyr-133/webpack-demo/tree/master/10-webpack_dev_server](https://github.com/zephyr-133/webpack-demo/tree/master/10-webpack_dev_server)
* 开发过程：

  1. 安装

      ```shell
      npm i webpack-dev-server -D
      ```
  2. 修改入口代码文件，添加module.hot.accept()授权热更新;

      ```javascript
      import React from 'react';
      import ReactDOM from 'react-dom/client';
      import './index.less';
      import favicon from '/public/images/favicon.png'

      if(module.hot) {
          module.hot.accept();
      }

      const root = ReactDOM.createRoot(document.getElementById('root'));

      class HelloWorld extends React.Component {
          render() {
              return <div className='hello-text'>
                  Hello World change wds
                  <img src={favicon} />
              </div>
          }
      }

      root.render(<HelloWorld />)
      ```
  3. 在webpack中添加devServer设置，将dist目录下的文件server到localhost:8080下

      ```javascript
      const path = require('path');

      module.exports = {
          entry: './src/index.js',
          output: {
              filename: "bundle.js",
              path: path.join(__dirname, 'dist'),
          },
          mode: 'development',
          devServer: {
              static: './dist'
          },
          module: {
              rules: [
                  {
                      test: /\.js$/,
                      use: 'babel-loader'
                  },
                  {
                      test: /\.less$/,
                      use: [
                          'style-loader',
                          'css-loader',
                          'less-loader'
                      ]
                  },
                  {
                      test: /\.(png|jpg|gif|jpeg)$/,
                      type: 'asset/resource',
                      generator: {
                          filename: './assets/images/[name][ext]',
                      },
                  },
                  {
                      test: /\.(eot|ttf|otf|woff(2)?)(\?[a-z0-9]+)?$/,
                      type: 'asset/resource',
                      generator: {
                          filename: './assets/fonts/[name][ext]',
                      },
                  }
              ]
          }
      };
      ```

      > webpack-dev-server 在编译之后不会写入到任何输出文件。而是将 bundle 文件保留在内存中，然后将它们 serve 到 server 中，就好像它们是挂载在 server 根路径上的真实文件一样。如果你的页面希望在其他不同路径中找到 bundle 文件，则可以通过 dev server 配置中的 [devMiddleware.publicPath](https://webpack.docschina.org/configuration/dev-server/#devserverdevmiddleware) 选项进行修改。
      >
      > 从 webpack-dev-server v4 开始，HMR 是默认启用的。它会自动应用 `webpack.HotModuleReplacementPlugin`​，这是启用 HMR 所必需的。因此当 `hot`​ 设置为 `true`​ 或者通过 CLI 设置 `--hot`​，你不需要在你的 `webpack.config.js`​ 添加该插件。查看 [HMR concepts page](https://webpack.docschina.org/concepts/hot-module-replacement/) 以获取更多信息。
      >
  4. 添加一个可以直接运行dev server的script：

      ```
      {
        "name": "webpack-demo",
        "version": "1.0.0",
        "description": "",
        "main": "index.js",
        "scripts": {
          "test": "echo \"Error: no test specified\" && exit 1",
          "build": "webpack",
      +    "start": "webpack serve --open",
          "watch": "webpack --watch"
        },
        "author": "",
        "license": "ISC",
        "devDependencies": {
          "@babel/core": "^7.22.11",
          "@babel/preset-env": "^7.22.14",
          "@babel/preset-react": "^7.22.5",
          "babel-loader": "^9.1.3",
          "css-loader": "^6.8.1",
          "less-loader": "^11.1.3",
          "react": "^18.2.0",
          "react-dom": "^18.2.0",
          "style-loader": "^3.3.3",
          "webpack": "^5.88.2",
          "webpack-cli": "^5.1.4",
          "webpack-dev-server": "^4.15.1"
        }
      }
      ```

## 使用webpack-dev-middleware

webpack-dev-middle是一个封装器（wrapper），它可以把webpack处理过的文件夹发送到一个server。webpack-dev-server在内部使用了它，然后它也可以作为一个单独的package使用，以便根据需求进行更多的自定义设置。下面是一个webpack-dev-middleware配置express server的示例。

* 案例地址：[https://github.com/zephyr-133/webpack-demo/tree/master/11-webpack_dev_middleware_express](https://github.com/zephyr-133/webpack-demo/tree/master/11-webpack_dev_middleware_express)
* 开发过程

  1. 安装：

      ```shell
      npm i express webpack-dev-middleware -D
      ```
  2. 新增server.js文件

      ```javascript
      const express = require('express');
      const webpack = require('webpack');
      const webpackDevMiddleware = require('webpack-dev-middleware');
      const path = require('path');

      const app = express();
      const config = require('./webpack.config.js');
      const compiler = webpack(config);

      //告知express使用webpack-dev-middleware，
      //以及将webpack.config.js配置文件作为基础配置。
      app.use(webpackDevMiddleware(compiler,{
          publicPath: config.output.publicPath
      }));

      // 添加一个路由来处理根路径请求，返回dist目录下的index.html
      // 如果你已使用了html-webpack-plugin，则可以跳过此设置
      app.get('/', function(req, res) {
          res.sendFile(path.join(__dirname, 'dist', 'index.html'));
      });

      app.listen(3000,function(){
          console.log('Example app listening on port 3000!\n');
      });
      ```
  3. 添加node server.js启动命令

      ```json
      {
        "name": "webpack-demo",
        "version": "1.0.0",
        "description": "",
        "main": "index.js",
        "scripts": {
          "test": "echo \"Error: no test specified\" && exit 1",
          "build": "webpack",
          "start": "webpack serve --open",
          "watch": "webpack --watch",
          "server": "node server.js"
        },
        "author": "",
        "license": "ISC",
        "devDependencies": {
          "@babel/core": "^7.22.11",
          "@babel/preset-env": "^7.22.14",
          "@babel/preset-react": "^7.22.5",
          "babel-loader": "^9.1.3",
          "css-loader": "^6.8.1",
          "express": "^4.18.2",
          "html-webpack-plugin": "^5.5.3",
          "less": "^4.2.0",
          "less-loader": "^11.1.3",
          "react": "^18.2.0",
          "react-dom": "^18.2.0",
          "style-loader": "^3.3.3",
          "webpack": "^5.88.2",
          "webpack-cli": "^5.1.4",
          "webpack-dev-middleware": "^6.1.1",
          "webpack-dev-server": "^4.15.1"
        }
      }
      ```

## 使用热更新-hot module replacement

模块热替换(HMR - hot module replacement)功能会在应用程序运行过程中，替换、添加或删除模块，而无需重新加载整个页面。

> 主要是通过以下几种方式，来显著加快开发速度：
>
> * 保留在完全重新加载页面期间丢失的应用程序状态。
> * 只更新变更内容，以节省宝贵的开发时间。
> * 在源代码中 CSS/JS 产生修改时，会立刻在浏览器中进行更新，这几乎相当于在浏览器 devtools 直接更改样式。

* 案例地址：[https://github.com/zephyr-133/webpack-demo/tree/master/12-webpack_dev_middleware_express_HMR](https://github.com/zephyr-133/webpack-demo/tree/master/12-webpack_dev_middleware_express_HMR)
* 开发过程：

  1. 安装：通过上面例子，我们已知webpack-dev-server从4.0.0之后默认开启了内置HMR，下面我们来示范使用webpack-dev-middleware的例子如何启用HMR

      ```
      npm i webpack-hot-middleware -D
      ```
  2. 修改server.js文件，添加webpack-hot-middleware处理

      ```javascript
      const express = require('express');
      const app = express();

      const webpack = require('webpack');
      const config = require('./webpack.config.js');
      const compiler = webpack(config);

      const path = require('path');

      //告知express使用webpack-dev-middleware，
      //以及将webpack.config.js配置文件作为基础配置。
      app.use(require('webpack-dev-middleware')(compiler,{
          publicPath: config.output.publicPath
      }));
      app.use(require("webpack-hot-middleware")(compiler));

      // 添加一个路由来处理根路径请求，返回dist目录下的index.html
      // 如果你已使用了html-webpack-plugin，则可以跳过此设置
      app.get('/', function(req, res) {
          res.sendFile(path.join(__dirname, 'dist', 'index.html'));
      });

      app.listen(3000,function(){
          console.log('Example app listening on port 3000!\n');
      });
      ```
  3. 修改webpack配置，添加HMR插件及webpack-hot-middleware入口文件，即：HMR Runtime

      ```javascript
      const path = require('path');
      const webpack = require('webpack');
      module.exports = {
          entry: {
              main: ['webpack-hot-middleware/client', './src/index.js']
          },
          output: {
              filename: "bundle.js",
              path: path.join(__dirname, 'dist'),
              publicPath: '/'
          },
          mode: 'development',
          module: {
              rules: [
                  {
                      test: /\.js$/,
                      use: 'babel-loader'
                  },
                  {
                      test: /\.less$/,
                      use: [
                          'style-loader',
                          'css-loader',
                          'less-loader'
                      ]
                  },
                  {
                      test: /\.(png|jpg|gif|jpeg)$/,
                      type: 'asset/resource',
                      generator: {
                          filename: './assets/images/[name][ext]',
                      },
                  },
                  {
                      test: /\.(eot|ttf|otf|woff(2)?)(\?[a-z0-9]+)?$/,
                      type: 'asset/resource',
                      generator: {
                          filename: './assets/fonts/[name][ext]',
                      },
                  }
              ]
          },
          plugins: [
              new webpack.HotModuleReplacementPlugin(),
          ],
      };
      ```
  4. 修改入口代码文件，添加module.hot.accept();

      ```javascript
      import React from 'react';
      import ReactDOM from 'react-dom/client';
      import './index.less';
      import favicon from '/public/images/favicon.png'

      if(module.hot) {
          module.hot.accept();
      }

      const root = ReactDOM.createRoot(document.getElementById('root'));

      class HelloWorld extends React.Component {
          render() {
              return <div className='hello-text'>
                  Hello World change webpack-dev-middleware HMR
                  <img src={favicon} />
              </div>
          }
      }

      root.render(<HelloWorld />)
      ```

> 如果你对HMR的原理感兴趣，推荐这篇文章：[**Webpack HMR 原理解析**](https://zhuanlan.zhihu.com/p/30669007)

## **代码压缩**

下面我们通过代码案例了解一下Web常见资源文件的基本压缩技巧，以帮助我们减少Web文件的体积，网络带宽占用，服务器压力及提高页面渲染速度等需求。

### HTML文件的压缩

html-webpack-plugin会简化HTML文件的创建，自动将打包后的JS和CSS文件注入到生成的HTML文件中，并且在production模式默认启用压缩。

> 注：如果需要更丰富的HTML压缩选项，可以还使用html-minimizer-webpack-plugin插件。

* 案例地址：[https://github.com/zephyr-133/webpack-demo/tree/master/13-minimizer_html_js](https://github.com/zephyr-133/webpack-demo/tree/master/13-minimizer_html_js)
* 开发过程：

  1. 安装

      ```shell
      npm i html-webpack-plugin -D
      ```
  2. 在webpack配置添加插件设置，mode设置为production

      ```javascript
      const HtmlWebpackPlugin = require('html-webpack-plugin');

      module.exports = {
          mode: 'production',
          plugins: [
              new HtmlWebpackPlugin({
                  template: './src/index.html',
                  filename: 'index.html',
              }),
          ]
      };
      ```

### JS文件的压缩

webpack默认内置了TerserPlugin，且在production模式默认启用，无需额外配置，代替了早期的UglifyPlugin（不支持es6）

> 因为在上一个案例中已启用production模式对js文件进行压缩，此处没有额外的案例提供。

### CSS文件的分离及压缩

基础的CSS文件打包，我们一般会使用style-loader进行处理，这种处理方式最终的打包结果就是CSS代码会内嵌到JS代码中。

而通常我们会将CSS代码从打包结果中分离出来，这时我们可以借助mini-css-extract-plugin完成，然后借助css-minimizer-webpack-plugin完成独立出来的CSS文件压缩。

> 注：optimize-css-assets-webpack-plugin不再支持webpack5

* 案例地址：[https://github.com/zephyr-133/webpack-demo/tree/master/14-minimizer_css](https://github.com/zephyr-133/webpack-demo/tree/master/14-minimizer_css)
* 开发过程：

  1. 安装

      ```shell
      npm i css-minimizer-webpack-plugin mini-css-extract-plugin -D
      ```
  2. 在webpack配置的optimization对象的minimizer属性中配置css压缩插件

      > 注：由于我们手动设置了minimizer，webpack认为我们需要使用自定义的压缩器插件，原本内部的JS压缩器就会失效，我们需要手动添加回来：
      >
      > ```shell
      > npm i css-minimizer-webpack-plugin -D
      > ```
      >

      ```javascript
      const path = require('path');
      const HtmlWebpackPlugin = require('html-webpack-plugin');
      const MiniCssExtractPlugin = require('mini-css-extract-plugin');
      const CssMinimizerPlugin = require('css-minimizer-webpack-plugin');
      const TerserWebpackPlugin = require('terser-webpack-plugin');

      module.exports = {
          module: {
              rules: [
                  {
                      test: /\.less$/,
                      use: [
                          MiniCssExtractPlugin.loader,
                          'css-loader',
                          'less-loader'
                      ]
                  }   
              ]
          },
          plugins: [
              new HtmlWebpackPlugin({
                  template: './src/index.html',
                  filename: 'index.html',
              }),
              new MiniCssExtractPlugin({
                  filename: 'styles.css',
              }),
          ],
          optimization: {
              minimize: true,
              minimizer: [
                  new CssMinimizerPlugin(),
                  new TerserWebpackPlugin()
              ]
          },
      };
      ```

## **导出设置及文件指纹**

文件指纹也称版本哈希或内容哈希，它是一个独一无二的标识符，用于识别特定文件的内容或版本，文件指纹通常以哈希值的形式表示，可以使用不同的哈希算法生成，例如MD5、SHA-1、SHA-256等，这对我们的Web应用制定和控制缓存策略十分有帮助。

> 文件指纹及常用占位符参考：[https://webpack.docschina.org/configuration/output/#template-strings](https://webpack.docschina.org/configuration/output/#template-strings)

* 案例地址：[https://github.com/zephyr-133/webpack-demo/tree/master/15-file_hash_with_clean](https://github.com/zephyr-133/webpack-demo/tree/master/15-file_hash_with_clean)
* 开发过程

  * 在webpack配置中添加文件指纹

    > 由于chunkhash和webpack打包的chunk相关，不同的entry生成不同的chunkhash值，所以JS文件我们一般使用chunkhash标识
    >
    > 而Contenthash根据文件内容来定义hash，文件内容不变，则contenthash不变，为了避免js改动影响css文件的打包，所以CSS文件我们一般使用contenthash标识
    >

    > webpack5还内置了clean:true属性来清理导出文件夹防止导出文件堆积过多，之前的版本则需要借助clean-webpack-plugin插件或者npm scripts命令（如："rm -rf ./dist && webpack"）实现。
    >

    ```javascript
    module.exports = {
        output: {
            filename: "[name]_[chunkhash].js",
            path: path.join(__dirname, 'dist'),
            clean:true
        },
        plugins: [
            new MiniCssExtractPlugin({
                filename: '[name]_[contenthash].css',
            }),
        ],

    };
    ```

> 参考链接：
>
> * [https://webpack.docschina.org/configuration/](https://webpack.docschina.org/configuration/)
> * [https://v4.webpack.docschina.org/loaders/](https://v4.webpack.docschina.org/loaders/)
> * [https://webpack.docschina.org/loaders/](https://webpack.docschina.org/loaders/)
> * [https://v4.webpack.docschina.org/plugins/](https://v4.webpack.docschina.org/plugins/)
> * [https://webpack.docschina.org/plugins/](https://webpack.docschina.org/plugins/)
> * [https://github.com/mishoo/UglifyJS](https://github.com/mishoo/UglifyJS)
> * [https://github.com/terser/terser](https://github.com/terser/terser)
> * [https://github.com/webpack-contrib/terser-webpack-plugin](https://github.com/webpack-contrib/terser-webpack-plugin)
> * [https://github.com/babel/minify#benchmarks](https://github.com/babel/minify#benchmarks)

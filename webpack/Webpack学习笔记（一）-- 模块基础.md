# Webpack学习笔记（一）-- 模块基础

不同于早期我们直接编写JavaScript、CSS、Html来进行开发Web应用。

如今的前端应用通常采用了模块化系统（CommonJs/ES6 Module/...）、开发框架（React/Vue/Angular/...)、开发语言（ES6/TypeScript/SASS/LESS/...）等进行开发。

而这些现代化的技术和框架都需要使用像Webpack一类的构建工具来处理一系列的工程化问题，如：代码转换、文件压缩、资源分包、模块合并、代码发布等。

在介绍Webapck之前，因为前端模块化系统的发展与Webpack一类的模块化构建打包工具息息相关，特别是对CommonJs和ES6模块的使用，下面内容将先介绍前端JS模块化发展的相关内容和后续文章内容导航。

# JS模块化发展

## 模块化的需求

1995年，第一版JavaScript发布，最初JS作为脚本语言，并没有模块化的实现，它仅仅用于制作飘落的雪花动画或者是用于表单验证，让所有代码都可以在同一个全局作用域下存在和交互，并未关心代码和依赖的隔离。

但随着 JavaScript 逐渐转变为一种通用语言，它开始被用于在各种环境（浏览器，移动设备，服务器，物联网）中构建复杂的应用程序。通过全局作用域进行程序组件交互的旧方法变得不可靠，因为代码量的增加往往会使应用程序过于脆弱。

这就是为什么为了简化创建 JavaScript 应用程序而创建了各种模块化的实现。

## 模块化的作用和定义

在软件设计中，模块化编程是将软件分解为若干独立的、可替换的、具有预定功能的模块，每个模块实现一个功能，各模块通过接口组合在一起，形成最终程序。

模块化设计旨在提高软件系统的可维护性、可扩展性、可重用性、可测试性和并行开发能力。

在前端开发中，我们引用Webpack的模块化描述：

> 在模块化编程中，开发者将程序分解成离散功能的Chunk(discrete chunks of functionality)，并称之为模块。 每个模块具有比完整程序更小的接触面，使得校验、调试、测试轻而易举。精心编写的模块提供了可靠的抽象和封装界限，使得应用程序中每个模块都具有条理清楚的设计和明确的目的。
>
> 模块应该是职责单一、相互独立、低耦合的、高度内聚且可替换的离散功能块。

> 对前端工程、模块化、组件化作用和意义感兴趣的读者，还推荐这篇老文章：[https://github.com/fouber/blog/issues/10](https://github.com/fouber/blog/issues/10)

## 了解JS模块化发展历史的作用

在大部分浏览器都已经实现 ES6 模块化规范的今天，我们新开发的项目也基本都是 ES6 搭配 Webpack 等构建工具，在模块化发展中出现的许多中间产物都已成历史，很多同学并没有使用过。

但通过了解这段历史可以帮忙我们知道当时的模块化解决方案所处理的痛点和问题是什么。

而理解这些问题也更助于我们了解和掌握模块化解决方案本质是在做些什么，它的未来会有哪些发展方向，它需要构建工具提供什么支持。

## 发展历史及问题

这个小节，我们正式进入对JS模块化发展的历史介绍。

我按某个关键性技术出现的时间为节点将JS模块化发展分成三个阶段，这样划分主要基于下面两个目的：

1. 希望分阶段对出现的解决方案和问题做出简述和概括的方式，更利于大家的记忆。
2. 希望清晰的时间线能方便大家感知前端技术演变之快，新老交替的情况不断上演，对于如今手握更好解决方案的我们，能够更全面的看待这段历史及其从业者，消除对技术发展过程中一些解决方案的盲目轻视，避免产生一些“何不食肉糜”的疑问。

> 注意：这并非前端生态真的分成泾渭分明三个阶段，尽管每个阶段的有些过渡性技术已经淘汰，但是大部分的技术都在深深影响着下个阶段的技术发展和思考，并依然在不同规模不同需求的前端项目中保持着生命力。

### 1995-2009年-模块化萌芽期

首先，我们了解一下这一时期出现的一些重要技术：

> * 1996年，样式表标准CSS第一版发布。
> * 1997年，DHTML（Dynamic HTML，动态HTML）发布，允许动态改变网页内容。这标志着DOM模式（Document Object Model，文档对象模型）正式应用。
> * 1999年，IE 5部署了XMLHttpRequest接口，允许JavaScript发出HTTP请求，为后来大行其道的Ajax应用创造了条件。同时，ECMAScript 3.0版发布，成为JavaScript的通行标准，得到了广泛支持。
> * 2001年，Douglas Crockford提出了JSON格式，用于取代XML格式，进行服务器和网页之间的数据交换。JavaScript可以原生支持这种格式，不需要额外部署代码。
> * 2005年，Ajax方法（Asynchronous JavaScript and XML）正式诞生，Jesse James Garrett在《[Ajax: A New Approach to Web Applications](https://web.archive.org/web/20061107032631/http://www.adaptivepath.com/publications/essays/archives/000385.php)》一文中发明了这个词汇。它开始流行的标志是，2月份发布的Google Maps项目大量采用该方法。它几乎成了新一代网站的标准做法，促成了Web 2.0时代的来临。
> * 2006年，jQuery函数库诞生，作者为John Resig。jQuery为操作网页DOM结构提供了非常强大易用的接口，成为了使用最广泛的函数库，并且让JavaScript语言的应用难度大大降低，推动了这种语言的流行。
> * 2007年，Webkit引擎在iPhone手机中得到部署。它最初基于KDE项目，2003年苹果公司首先采用，2005年开源。这标志着JavaScript语言开始能在手机中使用了，意味着有可能写出在桌面电脑和手机中都能使用的程序。
> * 2008年，V8编译器诞生。这是Google公司为Chrome浏览器而开发的，它的特点是让JavaScript的运行变得非常快。它提高了JavaScript的性能，推动了语法的改进和标准化，改变外界对JavaScript的不佳印象。同时，V8是开源的，任何人想要一种快速的嵌入式脚本语言，都可以采用V8，这拓展了JavaScript的应用领域。

由上可见，这个阶段是前端生态的发展初期，它诞生了许多前端赖以生存的基础核心技术，这个阶段的JS语言逐步标准化，开发需求逐渐从静态网页到动态网页的过渡，开发模式也开始出现前后端分离及前端MVC架构等。

但受到这段阶段的开发模式的制约，前端社区更多的还是基于语言层面对Js模块化进行探索和优化，下面我们介绍一下当时模块化面临的主要问题及典型的解决方案：

#### **命名冲突**

* **概念**：因为自从JavaScript出现的那一刻起，它就使用全局对象window作为存储所有未使用var关键字定义的变量的地方。这导致了当页面引用多个JS文件，且多个JS文件中存在相同名称的全局变量时，就会存在命名冲突，导致变量被覆盖出现功能异常。如下所示：

  ```javascript
  // file greeting.js
  var helloInLang = {
      en: 'Hello world!',
      es: '¡Hola mundo!',
      ru: 'Привет мир!'
  };

  function writeHello(lang) {
      document.write(helloInLang[lang]);
  }

  // file hello.js
  function writeHello() {
      document.write('The script is broken');
  }
  ```
* **解决方案**：

  * **命名空间模式（2002）：** 使用[对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Object_initializer)进行模块封装，将模块的逻辑和数据驻留在该对象的属性中，而不污染全局环境。但要注意，模块内部的数据是可以被外部直接修改的，依然有代码和数据隔离的缺陷。如下：

    ```javascript
    // file app.js
    var app = {};

    // file greeting.js
    app.helloInLang = {
        en: 'Hello world!',
        es: '¡Hola mundo!',
        ru: 'Привет мир!'
    };

    // file hello.js
    app.writeHello = function (lang) {
        document.write(app.helloInLang[lang]);
    };
    ```
  * **模块模式（2003）：** 使用[IIFE](https://developer.mozilla.org/zh-CN/docs/Glossary/IIFE)和[闭包](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures)进行模块封装，利用IIFE函数返回一个模块对象，而模块对象又通过闭包访问对象的方法。这个模式用了闭包来封装数据和代码以解决变量污染问题。

    ```javascript
    var greeting = (function () {
        var module = {};

        var helloInLang = {
            en: 'Hello world!',
            es: '¡Hola mundo!',
            ru: 'Привет мир!'
        };

        module.getHello = function (lang) {
            return helloInLang[lang];
        };

        module.writeHello = function (lang) {
            document.write(module.getHello(lang))
        };

        return module;
    }());
    ```

#### **大型代码库的支持或依赖关系问题**

* **概念**：JavaScript 在构建大型应用程序的另一个不便是使用<script>标签在最常见的 ES5 浏览器环境中显式指定插件脚本。但为了应用程序的源代码的可维护性，我们往往会把它拆分成独立的部分，因此，文件的数量可能非常大。对于大量文件，手动控制脚本（即通过脚本标签在页面上放置脚本）变得非常繁琐，因为首先您必须记住在页面中放置必要的脚本，其次要保留标签的正确顺序，以便解决文件之间的所有依赖关系。这就造成了JS文件请求过多，依赖模糊，难以维护的问题。
* **解决方案：**

  * **直接定义依赖关系（1999）：** 这个方案是通过显式调用函数（也用于初始化加载的模块）来获取模块的代码。也就是说，在这种方法中依赖项是直接在代码中定义的。以[Dojo](https://dojotoolkit.org/) 1.6版本为例，参考代码如下：

    ```javascript
    // file greeting.js
    dojo.provide("app.greeting");

    app.greeting.helloInLang = {
        en: 'Hello world!',
        es: '¡Hola mundo!',
        ru: 'Привет мир!'
    };

    app.greeting.sayHello = function (lang) {
        return app.greeting.helloInLang[lang];
    };

    // file hello.js
    dojo.provide("app.hello");

    dojo.require('app.greeting');

    app.hello = function(x) {
        document.write(app.greeting.sayHello('es'));
    };
    ```

    由于当时 JS文件非常简单，模块化方式非常简单粗暴 —— 通过全局方法定义、引用模块。这种定义方式与Commonjs 非常神似，区别是 Commonjs 以文件作为模块，而这种方法可以在任何文件中定义模块，模块不与文件关联。
  * **模板定义依赖关系（2006）：** 这种模式主要是通过将特殊标签包含在目标文件中来定义依赖关系。可以通过模板化（[erb](https://github.com/ruby/erb)、[jinja](https://github.com/pallets/jinja)、[smarty](https://github.com/smarty-php/smarty)）和特殊的构建工具（例如 [borshik](https://github.com/borschik/borschik)）将此标签解析为实际代码。下面以 [borshik](https://github.com/borschik/borschik)为例：

    ```
    // file app.tmp.js

    /*borschik:include:../lib/main.js*/

    /*borschik:include:../lib/helloInLang.js*/

    /*borschik:include:../lib/writeHello.js*/

    // file main.js
    var app = {};

    // file helloInLang.js
    app.helloInLang = {
        en: 'Hello world!',
        es: '¡Hola mundo!',
        ru: 'Привет мир!'
    };

    // file writeHello.js
    app.writeHello = function (lang) {
        document.write(app.helloInLang[lang]);
    };
    ```

    这个方案仅适用于预生成步骤，从上面的例子可以看出，其实这个方案并没有从根本上改变依赖关系的处理模式，尽管我们无需再Html上管理<script>标签了，但我们仍要处理在app.tmp.js文件上使用其他的标签定义插件脚本及顺序的管理问题。这个方案更大的作用是在于聚合了多个脚本文件，减少了JS文件请求。
  * **外部定义依赖关系（2007）：** 这个模式主要是通过在外部文件中定义依赖关系图，比如：我们创建一个Package文件，在Package文件中定义了所有JS文件的引用以及它们之间的关系，然后在运行应用程序时，通过自定义脚本加载程序接收Package文件，读取所有的依赖项列表，加载它们并按正确的顺序放入页面中。  
    这个方案可以追溯到 2007 年的 [MooTools 1.1](https://github.com/mootools/mootools-core/blob/d4b15bdd4061d7012748bc2c9da7e70864e12bbb/scripts.json#L14)。在简单情况下，可以参考这个[示例](https://github.com/myshov/eddloader)。

    ```javascript
    // file deps.json
    {
        "files": {
            "main.js": ["sayHello.js"],
            "sayHello.js": ["helloInLang.js"],
            "helloInLang.js": []
        }
    }

    // file helloInLang.js
    var helloInLang = {
        en: 'Hello world!',
        es: '¡Hola mundo!',
        ru: 'Привет мир!'
    };

    // file sayHello.js
    function sayHello(lang) {
        return helloInLang[lang];
    }

    // file main.js
    console.log(sayHello('en'));
    ```

    这个模式虽然解决了脚本的顺序加载问题，但在可维护性上依然存在缺陷，毕竟你的项目中将存在一个单独的文件，当依赖项发生变化时就需要去维护该文件。
  * **注释定义依赖关系（2006）：** 它与**直接定义依赖关系**非常相似，但这个模式中我们不使用某种函数，而是使用注释，其中包含有关特定模块的所有依赖项的信息。  
    跟**模板定义依赖关系**一样，使用此模式的应用程序必须是预先构建的（这种方法在 2006 年用于 [Valerio Proietti](https://github.com/kamicane) 创建的 [MooTools](https://github.com/mootools/mootools-core/blob/41b0bdedce3adeb921c181145d7c79a8ecbf4763/Plugins/Fxpack.js#L12)），或者动态解析下载的代码并在运行时解析依赖项（这种方法用于 [LazyJS](https://github.com/bevacqua/lazyjs)，由 [Nicolás Bevacqua](https://github.com/bevacqua) 创建）。  
    以LayzJs为例，如下所示：

    ```javascript
    // file helloInLang.js
    var helloInLang = {
        en: 'Hello world!',
        es: '¡Hola mundo!',
        ru: 'Привет мир!'
    };

    // file sayHello.js

    /*! lazy require scripts/app/helloInLang.js */

    function sayHello(lang) {
        return helloInLang[lang];
    }

    // file hello.js

    /*! lazy require scripts/app/sayHello.js */

    document.write(sayHello('en'));
    ```

    这个方式是在源文件的注释中直接定义依赖项，之后借助脚本加载器分析文件的注释以查看它具有哪些依赖项，然后脚本加载器使用 XHR 来加载这些脚本文件。以这种方式加载依赖关系图后，可以使用 <script> 标记按顺序执行它们。

### 2009-2015年-模块系统爆发期

首先，我们了解一下这一时期出现的一些重要技术：

> * 2009年，Node.js项目诞生，创始人为Ryan Dahl，它标志着JavaScript可以用于服务器端编程，从此网站的前端和后端可以使用同一种语言开发。并且，Node.js可以承受很大的并发流量，使得开发某些互联网大规模的实时应用变得容易。
> * 2010年，三个重要的项目诞生，分别是NPM、BackboneJS和RequireJS，标志着JavaScript进入模块化开发的时代。
> * 2012年，微软发布TypeScript语言。该语言被设计成JavaScript的超集，这意味着所有JavaScipt程序，都可以不经修改地在TypeScript中运行。同时，TypeScript添加了很多新的语法特性，主要目的是为了开发大型程序，然后还可以被编译成JavaScript运行。
> * 2012年，单页面应用程序框架（single-page app framework）开始崛起，AngularJS项目和Ember项目都发布了1.0版本。
> * 2013年，Facebook发布UI框架库React，引入了新的JSX语法，使得UI层可以用组件开发。

在Node.JS之前，就已经有了[以JavaScript为主要语言的服务器端开发平台](https://en.wikipedia.org/wiki/Comparison_of_server-side_JavaScript_solutions)。由于缺乏适当的规范，服务器解决方案没有提供统一的 API 来与操作系统及其环境（文件系统、网络、环境变量等）进行通信，从而在代码分发方面产生问题。

2009年，NodeJS项目诞生，它是服务器上的 JavaScript 运行环境，提供了操作系统API，以此为转折点，JavaScript 成为与 Python 和 Ruby 一样的服务器脚本语言，也成为唯一的浏览器和服务器都支持的语言，前端Web应用的开发模式迎来了巨大变化，同时Node使用[CommonJS](https://wiki.mozilla.org/ServerJS)规范实现[NodeJS的模块体系](https://nodejs.org/docs/latest/api/modules.html#modules-commonjs-modules)也让模块化探索开始进入新的阶段。

#### 新的开发模式

* Node 环境下开发
* 大量使用服务器端工具
* 引入持续集成等软件工程的标准流程
* 开发完成后，编译成浏览器可以运行的脚本，放上 CDN

基于Node环境进行的开发，前端工程化开始初具规模，一套标准化的模块机制、版本管理、对外发布、持续集成的标准开发流程逐渐被推广流行。

#### Node模块系统

CommonJs作为一个在Node.js项目使用并长期作为服务端JS标准规范，深深影响了许许多多项目与第三方库的开发和发布，尽管如今它正在缓慢的被ESM取代，但我们依然有必要深入了解一下它带来了哪些变化和实现：

> 此节内容全部选自《[CommonJS规范](https://javascript.ruanyifeng.com/nodejs/module.html)》部分章节的内容整理，熟悉CommonJs或看过原文的可直接跳过这一部分。

1. **命名冲突和依赖关系的处理：**

    1. 所有代码都运行在模块作用域，不会污染全局作用域。
    2. 模块可以多次加载，但是只会在第一次加载时运行一次，然后运行结果就被缓存了，以后再加载，就直接读取缓存结果。要想让模块再次运行，必须清除缓存。
    3. 模块加载的顺序，按照其在代码中出现的顺序。
2. **模块、导入、导出的实现：**

    1. **模块对象：** Node内部提供一个`Module`​构建函数。所有模块都是`Module`​的实例。

        ```javascript
        function Module(id, parent) {
          this.id = id;
          this.exports = {};
          this.parent = parent;
          // ...
        ```

        每个模块内部，都有一个`module`​对象，代表当前模块。它有以下属性。

        * ​`module.id`​ 模块的识别符，通常是带有绝对路径的模块文件名。
        * ​`module.filename`​ 模块的文件名，带有绝对路径。
        * ​`module.loaded`​ 返回一个布尔值，表示模块是否已经完成加载。
        * ​`module.parent`​ 返回一个对象，表示调用该模块的模块。
        * ​`module.children`​ 返回一个数组，表示该模块要用到的其他模块。
        * ​`module.exports`​ 表示模块对外输出的值。
    2. **模块导出：** Node提供`exports`​与`module.exports`​两种方式，详情如下（如果你觉得它们很难区分，可以放弃使用`exports`​，只使用`module.exports`​）：

        1. **module.exports：** 表示当前模块对外输出的接口，其他文件加载该模块，实际上就是读取`module.exports`​变量。

            ```
            var EventEmitter = require('events').EventEmitter;
            module.exports = new EventEmitter();

            setTimeout(function() {
              module.exports.emit('ready');
            }, 1000);
            ```

            上面模块会在加载后1秒后，发出ready事件。其他文件监听该事件，可以写成下面这样。

            ```
            var a = require('./a');
            a.on('ready', function() {
              console.log('module a is ready');
            });
            ```
        2. **exports：** 指向module.exports，这是Node为了方便，每个模块提供的，但这等同在每个模块头部，有一行这样的命令。

            ```
            var exports = module.exports;
            ```

            造成的结果是，在对外输出模块接口时，可以向exports对象添加方法。

            ```
            exports.area = function (r) {
              return Math.PI * r * r;
            };

            exports.circumference = function (r) {
              return 2 * Math.PI * r;
            };
            ```

            > 注意，使用exports变量时，要防止切断`exports`​与`module.exports`​的联系。
            >
            > 例如：
            >
            > 1. 如果一个模块的对外接口，就是一个单一的值，不能使用`exports`​输出，否则`exports`​不再指向`module.exports`​，如下
            >
            >     ```javascript
            >     exports = function(x) {console.log(x)};
            >     ```
            > 2. ​`hello`​函数是无法对外输出的，因为`module.exports`​被重新赋值
            >
            >     ```javascript
            >     exports.hello = function() {
            >       return 'hello';
            >     };
            >
            >     module.exports = 'Hello world';
            >     ```
            >
    3. **模块导入：** Node提供一个`require`​命令，接受一个参数字符串，并加载文件或目录，如果没有发现指定目录和文件，则会报错。

        1. **文件加载规则：**

            1. **文件执行：** 加载文件返回该模块的exports对象。当模块输出是对象时，require命令获得一个对象。当模块输出是一个函数时，require命令可以调用自身立即执行，如下：

                ```javascript
                // example1.js
                var invisible = function () {
                  console.log("invisible");
                }

                exports.message = "hi";

                exports.say = function () {
                  console.log(message);
                }
                // example2.js
                module.exports = function () {
                  console.log("hello world")
                }

                //require.js
                var example = require('./example.js');
                example
                // {
                //   message: "hi",
                //   say: [Function]
                // }

                require('./example2.js')()
                ```
            2. **文件名后缀：** 如果指定的模块文件没有发现，Node会尝试为文件名添加`.js`​、`.json`​、`.node`​后缀，再去搜索。`.js`​件会以文本格式的JavaScript脚本文件解析，`.json`​文件会以JSON格式的文本文件解析，`.node`​文件会以编译后的二进制文件解析

                ```
                var foo = require('foo');
                //  等同于
                var foo = require('foo.js');
                ```
            3. **路径寻址：**   
                （1）以“/”开头，则表示加载的是一个位于绝对路径的模块文件。  
                （2）以“./”开头，则表示加载的是一个位于相对路径（跟当前执行脚本的位置相比）的模块文件。  
                （3）不以“./“或”/“开头，则表示加载的是一个默认提供的核心模块（位于Node的系统安装目录中），或者一个位于各级node_modules目录的已安装模块（全局安装或局部安装）。

                > 举例来说，脚本`/home/user/projects/foo.js`​执行了`require('bar.js')`​命令，Node会依次搜索以下文件。
                >
                > * /usr/local/lib/node/bar.js
                > * /home/user/projects/node_modules/bar.js
                > * /home/user/node_modules/bar.js
                > * /home/node_modules/bar.js
                > * /node_modules/bar.js
                >
                > 这样设计的目的是，使得不同的模块可以将所依赖的模块本地化。
                >
        2. **目录加载规则：**

            `require`​发现参数字符串指向一个目录，便会查找该目录下的入口文件以加载整个目录。

            1. 查看该目录下的`package.json`​文件，然后加载`main`​字段指定的入口文件，如下：

                ```
                // package.json
                { "name" : "some-library",
                  "main" : "./lib/some-library.js" }
                ```
            2. 如果`package.json`​文件没有`main`​字段，或者没有`package.json`​文件，则会加载该目录下的`index.js`​文件或`index.node`​文件。
        3. **模块的缓存：**

            > 注意，缓存是根据绝对路径识别模块的，如果同样的模块名，但是保存在不同的路径，`require`​命令还是会重新加载该模块。
            >
            > 同时，因为缓存的key是基于模块的绝对路径，所以在读取缓存前它需要根据id解析出路径(Module._resolveFilename)，这个操作并不是特别消耗性能，但在一些业务频繁需要动态调用模块场景下，在性能压测等高并发压力时，可考虑优化掉这个解析操作，提升缓存性能。
            >

            1. **加载：** 第一次加载某个模块时，Node会缓存该模块。以后再加载该模块，就直接从缓存取出该模块的`module.exports`​属性。

                ```
                require('./example.js');
                require('./example.js').message = "hello";
                require('./example.js').message
                // "hello"
                ```

                上面代码中，连续三次使用`require`​命令，加载同一个模块。第二次加载的时候，为输出的对象添加了一个`message`​属性。但是第三次加载的时候，这个message属性依然存在，这就证明`require`​命令并没有重新加载模块文件，而是输出了缓存。
            2. **删除：** 所有缓存的模块保存在`require.cache`​之中，删除模块缓存的操作如下：

                ```
                // 删除指定模块的缓存
                delete require.cache[moduleName];

                // 删除所有模块的缓存
                Object.keys(require.cache).forEach(function(key) {
                  delete require.cache[key];
                })
                ```
        4. **加载机制：** CommonJS模块的加载机制是，输入的是被输出的值的拷贝。也就是说，一旦输出一个值，模块内部的变化就影响不到这个值。  
            下面是一个模块文件`lib.js`​。

            ```javascript
            // lib.js
            var counter = 3;
            function incCounter() {
              counter++;
            }
            module.exports = {
              counter: counter,
              incCounter: incCounter,
            };
            ```

            上面代码输出内部变量`counter`​和改写这个变量的内部方法`incCounter`​。

            然后，加载上面的模块。

            ```javascript
            // main.js
            var counter = require('./lib').counter;
            var incCounter = require('./lib').incCounter;

            console.log(counter);  // 3
            incCounter();
            console.log(counter); // 3
            ```

            上面代码说明，`counter`​输出以后，`lib.js`​模块内部的变化就影响不到`counter`​了。
        5. **内部处理流程：**

            ​`require`​命令是CommonJS规范之中，用来加载其他模块的命令。它其实不是一个全局命令，而是指向当前模块的`module.require`​命令，而后者又调用Node的内部命令`Module._load`​。

            ```
            Module._load = function(request, parent, isMain) {
              // 1. 检查 Module._cache，是否缓存之中有指定模块
              // 2. 如果缓存之中没有，就创建一个新的Module实例
              // 3. 将它保存到缓存
              // 4. 使用 module.load() 加载指定的模块文件，
              //    读取文件内容之后，使用 module.compile() 执行文件代码
              // 5. 如果加载/解析过程报错，就从缓存删除该模块
              // 6. 返回该模块的 module.exports
            };
            ```

            上面的第4步，采用`module.compile()`​执行指定模块的脚本，逻辑如下。

            ```
            Module.prototype._compile = function(content, filename) {
              // 1. 生成一个require函数，指向module.require
              // 2. 加载其他辅助方法到require
              // 3. 将文件内容放到一个函数之中，该函数可调用 require
              // 4. 执行该函数
            };
            ```

            上面的第1步和第2步，`require`​函数及其辅助方法主要如下。

            * ​`require()`​: 加载外部模块
            * ​`require.resolve()`​：将模块名解析到一个绝对路径
            * ​`require.main`​：指向主模块
            * ​`require.cache`​：指向所有缓存的模块
            * ​`require.extensions`​：根据文件的后缀名，调用不同的执行函数

            一旦`require`​函数准备完毕，整个所要加载的脚本内容，就被放到一个新的函数之中，这样可以避免污染全局环境。该函数的参数包括`require`​、`module`​、`exports`​，以及其他一些参数。

            ```
            (function (exports, require, module, __filename, __dirname) {
              // YOUR CODE INJECTED HERE!
            });
            ```

            ​`Module._compile`​方法是同步执行的，所以`Module._load`​要等它执行完成，才会向用户返回`module.exports`​的值。

#### 第三方模块系统

Node生态的繁荣发展诞生了大量的JS项目，这些项目都在Node环境中参照CommonJs规范实现的模块系统下开发，这也标志"Javascript模块化编程"正式诞生。

但参照[CommonJS](https://wiki.mozilla.org/ServerJS)规范实现的[模块体系](https://nodejs.org/docs/latest/api/modules.html#modules-commonjs-modules)依托于Node平台，而在这一时期的浏览器原生并不支持任何模块化系统。同时，因为Node的模块系统是服务端模块系统，可以直接使用require命令同步加载读取本机模块文件，而浏览器的模块文件需要依赖网络进行异步读取，当前的CommonJs规范并不适用于浏览器端的模块系统。

为了拥有客户端的模块系统，前端社区诞生了以AMD，CMD规范为基准的模块加载器（[RequireJS](https://github.com/requirejs/requirejs)、[SeaJs](https://github.com/seajs/seajs)）来辅助浏览器的模块系统实现。

为了使用一套模块规范就能兼容服务端和客户端的模块化开发，前端社区在后来还推出了融合CommonJs和AMD的跨平台方案UMD规范。

> 这段历史中出现的相关技术和规范在此处不详细展开了，感兴趣的同学自行查阅。

### 2015-至今，ES6 模块标准普及期

在上个时期，模块化编程已经开始在前端社区普及，但是多种多样的第三方模块化规范及其不同实现和应用，也造成了前端生态开始逐渐混乱的模块化开发模式，前端社区急需一个官方统一的模块化标准出现，下面我们看下这一时期的技术有了哪些变化：

> * 2015年3月，Facebook公司发布了React Native项目，将React框架移植到了手机端，可以用来开发手机App。它会将JavaScript代码转为iOS平台的Objective-C代码，或者Android平台的Java代码，从而为JavaScript语言开发高性能的原生App打开了一条道路。
> * 2015年6月，ECMA标准化组织正式批准了ECMAScript 6语言标准，定名为《ECMAScript 2015 标准》。JavaScript语言正式进入了下一个阶段，成为一种企业级的、开发大规模应用的语言。这个标准从提出到批准，历时10年，而JavaScript语言从诞生至今也已经20年了。
> * 2015年6月，Mozilla 在 asm.js 的基础上发布 WebAssembly 项目。这是一种JavaScript语言编译后的二进制格式，类似于Java的字节码，有利于移动设备加载JavaScript脚本，解析速度提高了20+倍。这意味着将来的软件，会发布JavaScript二进制包。
> * 2017年11月，所有主流浏览器全部支持 ES6。（参考：[ES6](https://caniuse.com/?search=es6)）
> * 2017年6月，《ECMAScript 2017 标准》发布，正式引入了 async 函数，使得异步操作的写法出现了根本的变化。
> * 2017年9月，Node.js 发布的 8.5.0 版本开始支持 ES6 Module。只不过是处于实验阶段。需要添加 `--experimental-modules`​ 参数。
> * 2017年11月，所有主流浏览器全部支持 WebAssembly，这意味着任何语言都可以编译成 JavaScript，在浏览器运行。
> * 2018年5月，所有主流浏览器全部支持以` <script type="module">`​方式引用 ES6 Module的代码，意味着浏览器开始原生支持模块系统。（参考：[ESM](https://caniuse.com/?search=esm)）
> * 2019年11月，Node.js 发布的 13.2.0 版本中取消了 `--experimental-modules`​ 参数 ，也就是说从 v13.2 版本开始，Node.js 已经默认打开了 ES6 Module 的支持。
> * 2020年6月，《ECMAScript 2020 标准》发布，正式发布将2016年TC39开始起草[Import()函数提案](https://github.com/tc39/proposal-dynamic-import)，为ES6 Module新增动态导入支持。同时，在2020年1月，所有主流浏览器已全部支持该特性。（参考：[Dynamic Import](https://caniuse.com/?search=dynamic%20import)）
> * 2023年5月，所有主流浏览器全部支持 2018年WICG开始起草的[模块标识符映射提案](https://github.com/WICG/import-maps)，进一步增加浏览器对ES6 Module 开发的支持。（参考：[Import-Maps](https://caniuse.com/?search=import-map)）

2015年TC39委员会随规范ES2015首次发布官方模块系统ES6 Module，简称ESM。这是ES6 在语言标准的层面上实现的模块功能，完全可以取代 CommonJS 和 AMD 规范，成为浏览器和服务器通用的模块解决方案。

下面让我们来深入了解一下ES6模块的设计和应用。

> 下面ESM的相关内容全部选自下面两篇文章中部分章节的内容整理，熟悉ESM或看过原文的可直接跳过这一部分。
>
> * [Module 的语法](https://es6.ruanyifeng.com/#docs/module)
> * [Module 的加载实现](https://es6.ruanyifeng.com/#docs/module-loader)

#### ESM设计思想

ES6 模块的设计思想是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。

> CommonJS 和 AMD 模块，都只能在运行时确定这些东西。比如，CommonJS 模块就是对象，输入时必须查找对象属性。
>
> ```javascript
> // CommonJS模块
> let { stat, exists, readfile } = require('fs');
>
> // 等同于
> let _fs = require('fs');
> let stat = _fs.stat;
> let exists = _fs.exists;
> let readfile = _fs.readfile;
> ```
>
> 上面代码的实质是整体加载`fs`​模块（即加载`fs`​的所有方法），生成一个对象（`_fs`​），然后再从这个对象上面读取 3 个方法。这种加载称为“运行时加载”，因为只有运行时才能得到这个对象，导致完全没办法在编译时做“静态优化”。
>
> ES6 模块不是对象，而是通过`export`​命令显式指定输出的代码，再通过`import`​命令输入。
>
> ```javascript
> // ES6模块
> import { stat, exists, readFile } from 'fs';
> ```
>
> 上面代码的实质是从`fs`​模块加载 3 个方法，其他方法不加载。这种加载称为“编译时加载”或者静态加载，即 ES6 可以在编译时就完成模块加载，效率要比 CommonJS 模块的加载方式高。当然，这也导致了没法引用 ES6 模块本身，因为它不是对象。

由于 ES6 模块是编译时加载，使得静态分析成为可能。有了它，就能进一步拓宽 JavaScript 的语法，比如引入宏（macro）和类型检验（type system）这些只能靠静态分析实现的功能。

除了静态加载带来的各种好处，ES6 模块还有以下好处。

* 不再需要`UMD`​模块格式了，将来服务器和浏览器都会支持 ES6 模块格式。目前，通过各种工具库，其实已经做到了这一点。
* 将来浏览器的新 API 就能用模块格式提供，不再必须做成全局变量或者`navigator`​对象的属性。
* 不再需要对象作为命名空间（比如`Math`​对象），未来这些功能可以通过模块提供。

#### ESM的导出

在ESM中使用`export`​命令规定模块的对外接口。

一个模块就是一个独立的文件，该文件内部的所有变量，外部无法获取。如果你希望外部能够读取模块内部的某个变量，就必须使用`export`​关键字输出该变量。

* **导出方法：**

  1. ​`export`​命令直接输出变量定义表达式：

      ```javascript
      // profile.js
      export var firstName = 'Michael';
      export var lastName = 'Jackson';
      export var year = 1958;
      ```
  2. ​`export`​命令后面，使用大括号指定所要输出的一组变量：

      ```javascript
      // profile.js
      var firstName = 'Michael';
      var lastName = 'Jackson';
      var year = 1958;

      export { firstName, lastName, year };
      ```

      > 优先考虑使用这种写法。因为这样就可以在脚本尾部，一眼看清楚输出了哪些变量
      >
  3. 使用`export default`​命令，为模块指定默认输出。因为一个模块只能有一个默认输出，所以`export default`​命令只能使用一次。

      ```javascript
      // export-default.js
      export default function () {
        console.log('foo');
      }
      ```

      上面代码是一个模块文件`export-default.js`​，它的默认输出是一个函数。

      ​`export default`​命令用在非匿名函数前，也是可以的。

      ```javascript
      // export-default.js
      export default function foo() {
        console.log('foo');
      }

      // 或者写成

      function foo() {
        console.log('foo');
      }

      export default foo;
      ```

      上面代码中，`foo`​函数的函数名`foo`​，在模块外部是无效的。加载的时候，视同匿名函数加载。

      > 本质上，`export default`​就是输出一个叫做`default`​的变量或方法，然后系统允许你为它取任意名字。所以，下面的写法是有效的。
      >
      > ```javascript
      > // modules.js
      > function add(x, y) {
      >   return x * y;
      > }
      > export {add as default};
      > // 等同于
      > // export default add;
      >
      > // app.js
      > import { default as foo } from 'modules';
      > // 等同于
      > // import foo from 'modules';
      > ```
      >
      > 正是因为`export default`​命令其实只是输出一个叫做`default`​的变量，所以它后面不能跟变量声明语句。
      >
      > ```javascript
      > // 正确
      > export var a = 1;
      >
      > // 正确
      > var a = 1;
      > export default a;
      >
      > // 错误
      > export default var a = 1;
      > ```
      >
      > 上面代码中，`export default a`​的含义是将变量`a`​的值赋给变量`default`​。所以，最后一种写法会报错。
      >
      > 同样地，因为`export default`​命令的本质是将后面的值，赋给`default`​变量，所以可以直接将一个值写在`export default`​之后。
      >
      > ```javascript
      > // 正确
      > export default 42;
      >
      > // 报错
      > export 42;
      > ```
      >
      > 上面代码中，后一句报错是因为没有指定对外的接口，而前一句指定对外接口为`default`​。
      >
* **导出类型：** 目前，export 命令能够对外输出的就是三种接口：函数（Functions）， 类（Classes），var、let、const 声明的变量（Variables）。
* **导出重命名：**

  ​`export`​输出的变量就是本来的名字，但是可以使用`as`​关键字重命名。

  ```javascript
  function v1() { ... }
  function v2() { ... }

  export {
    v1 as streamV1,
    v2 as streamV2,
    v2 as streamLatestVersion
  };
  ```
* **导出特性：**

  1. 与CommonJs不同，ESM导出的是值的动态引用，而非值的拷贝，并且不会缓存值，模块里面的变量绑定其所在的模块。因此，我们是可以获取到ESM模块内部实时的变化。

      ```javascript
      //test.mjs
      export var foo = 'bar';
      setTimeout(() => foo = 'baz', 500);
      ```

      ```javascript
      import { foo } from './test.mjs'
      console.log(foo)
      setTimeout(() => console.log(foo) , 1000);
      ```
  2. ​`export`​通过接口，输出的是同一个值。不同的脚本加载这个接口，得到的都是同样的实例。

      ```javascript
      // mod.js
      function C() {
        this.sum = 0;
        this.add = function () {
          this.sum += 1;
        };
        this.show = function () {
          console.log(this.sum);
        };
      }

      export let c = new C();
      ```

      上面的脚本`mod.js`​，输出的是一个`C`​的实例。不同的脚本加载这个模块，得到的都是同一个实例。

      ```javascript
      // x.js
      import {c} from './mod';
      c.add();

      // y.js
      import {c} from './mod';
      c.show();

      // main.js
      import './x';
      import './y';
      ```

      现在执行`main.js`​，输出的是`1`​。

      ```bash
      $ babel-node main.js
      1
      ```

      这就证明了`x.js`​和`y.js`​加载的都是`C`​的同一个实例。
* **导出命令使用位置：**

  ​`export`​命令可以出现在模块的任何位置，只要处于模块顶层就可以。如果处于块级作用域内，就会报错。这是因为处于条件代码块之中，就没法做静态优化了，违背了 ES6 模块的设计初衷。

  ```javascript
  function foo() {
    export default 'bar' // SyntaxError
  }
  foo()
  ```

#### ESM的导入

1. **使用**​`import`​**命令静态导入其他模块提供的功能。（编译时加载）**

    * **导入方法：**

      1. ​`import`​命令接受一对大括号，里面指定要从其他模块导入的变量名。

          ```javascript
          // main.js
          import { firstName, lastName, year } from './profile.js';

          function setName(element) {
            element.textContent = firstName + ' ' + lastName;
          }
          ```

          > 大括号里面的变量名，必须与被导入模块（`profile.js`​）对外接口的名称相同。
          >
      2. 使用`import`​命令加载模块默认输出，并为模块的默认输出指定任意名字。此时，`import`​命令后面才不用加大括号，因为只可能唯一对应`export default`​命令。

          ```javascript
          // import-default.js
          import customName from './export-default';
          customName(); // 'foo'
          ```
      3. ​`import`​后面的`from`​指定模块文件的位置，可以是相对路径，也可以是绝对路径。如果不带有路径，只是一个模块名，那么必须有配置文件，告诉 JavaScript 引擎该模块的位置。

          ```javascript
          import { myMethod } from 'util';
          ```

          上面代码中，`util`​是模块文件名，由于不带有路径，必须通过配置，告诉引擎怎么取到这个模块。

          > 由于`import`​是静态执行，所以不能使用表达式和变量，这些只有在运行时才能得到结果的语法结构。
          >
          > ```javascript
          > // 报错
          > import { 'f' + 'oo' } from 'my_module';
          >
          > // 报错
          > let module = 'my_module';
          > import { foo } from module;
          > ```
          >
      4. ​`import`​语句执行所加载的模块：

          ```javascript
          import 'lodash';
          ```

          上面代码仅仅执行`lodash`​模块，但是不输入任何值。

          > 如果多次重复执行同一句`import`​语句，那么只会执行一次，而不会执行多次。
          >
          > ```javascript
          > import 'lodash';
          > import 'lodash';
          > ```
          >
          > 上面代码加载了两次`lodash`​，但是只会执行一次。
          >
          > ```javascript
          > import { foo } from 'my_module';
          > import { bar } from 'my_module';
          >
          > // 等同于
          > import { foo, bar } from 'my_module';
          > ```
          >
          > 上面代码中，虽然`foo`​和`bar`​在两个语句中加载，但是它们对应的是同一个`my_module`​模块。也就是说，`import`​语句是 Singleton 模式。
          >
          > 目前阶段，通过 Babel 转码，CommonJS 模块的`require`​命令和 ES6 模块的`import`​命令，可以写在同一个模块里面，但是最好不要这样做。因为`import`​在静态解析阶段执行，所以它是一个模块之中最早执行的。下面的代码可能不会得到预期结果。
          >
          > ```javascript
          > require('core-js/modules/es6.symbol');
          > require('core-js/modules/es6.promise');
          > import React from 'React';
          > ```
          >
    * **导入重命名：**

      1. 如果想为输入的变量重新取一个名字，`import`​命令要使用`as`​关键字，将输入的变量重命名。

          ```javascript
          import { lastName as surname } from './profile.js';
          ```
      2. 除了指定加载某个输出值，还可以使用整体加载，即用星号（`*`​）指定一个对象，所有输出值都加载在这个对象上面。

          ```javascript
          import * as circle from './circle';

          console.log('圆面积：' + circle.area(4));
          console.log('圆周长：' + circle.circumference(14));
          ```
    * **导入特性：**

      ​`import`​命令输入的变量都是只读的，因为它的本质是导入变量的引用，所以不允许在加载模块的脚本里面，更改变量的引用的。

      ```javascript
      import {a} from './xxx.js'

      a = {}; // Syntax Error : 'a' is read-only;
      ```

      但是，因为导入的是变量的引用，当变量是一个对象时，在加载模块中编辑对象的属性是允许的。

      ```javascript
      import {a} from './xxx.js'

      a.foo = 'hello'; // 合法操作
      ```

      上面代码中，`a`​的属性可以成功改写，并且其他模块也可以读到改写后的值。不过，这种写法很难查错，建议凡是输入的变量，都当作完全只读，不要轻易改变它的属性。

      > 特别注意，模块整体加载所在的那个对象，应该是可以静态分析的，所以不允许运行时改变。
      >
      > 下面的写法都是不允许的。
      >
      > ```javascript
      > import * as circle from './circle';
      >
      > // 下面两行都是不允许的
      > circle.foo = 'hello';
      > circle.area = function () {};
      > ```
      >
    * **导入命令使用位置：**

      与`export`​相同，`import`​命令可以出现在模块的任何位置，只要处于模块顶层就可以。如果处于块级作用域内，就会报错。  
      但要注意，`import`​命令具有提升效果，会提升到整个模块的头部，首先执行。

      ```javascript
      foo();

      import { foo } from 'my_module';
      ```

      上面的代码不会报错，因为`import`​的执行早于`foo`​的调用。这种行为的本质是，`import`​命令是编译阶段执行的，在代码运行之前。
2. **使用**​`import()`​**命令动态导入其他模块提供的功能。（运行时加载+异步加载）**   
    [ES2020提案](https://github.com/tc39/proposal-dynamic-import) 引入`import()`​函数，支持动态加载模块。

    * **导入方法：**

      ​`import`​​函数的参数`specifier`​​，指定所要加载的模块的位置。`import`​​命令能够接受什么参数，`import()`​​函数就能接受什么参数，两者区别主要是后者为动态加载。

      ```javascript
      import(specifier)
      ```

      1. **解构：**   
          ​`import()`​​加载模块成功以后，这个模块会作为一个对象，当作`then`​​方法的参数。因此，可以使用对象解构赋值的语法，获取输出接口。

          ```javascript
          import('./myModule.js')
          .then(({export1, export2}) => {
            // ...·
          });

          async function main() {
             const {export1, export2} = await import('./myModule.js');
            //...
          }
          main();
          ```

          上面代码中，`export1`​​和`export2`​​都是`myModule.js`​​的输出接口，可以解构获得。
      2. **参数直接获得：**   
          如模块有`default`​​输出接口

          ```javascript
          import('./myModule.js')
          .then(myModule => {
            console.log(myModule.default);
          });

          async function main() {
            const myModule = await import('./myModule.js');
            console.log(myModule.default);
          }
          main();
          ```
      3. **具名输入：**   
          如模块有`default`​​输出接口

          ```javascript
          import('./myModule.js')
          .then(({default: theDefault}) => {
            console.log(theDefault);
          });


          async function main() {
            const {default: theDefault} = await import('./myModule.js');
            console.log(theDefault);
          }
          main();
          ```
      4. **同时加载多个模块：**

          ```javascript
          Promise.all([
            import('./module1.js'),
            import('./module2.js'),
            import('./module3.js'),
          ])
          .then(([module1, module2, module3]) => {
             ···
          });

          async function main() {
            const [module1, module2, module3] =
              await Promise.all([
                import('./module1.js'),
                import('./module2.js'),
                import('./module3.js'),
              ]);
          }
          main();
          ```
    * **导入特性：**

      1. ​`import()`​​返回一个 Promise 对象，需要使用`then()`​​方法指定处理函数。考虑到代码的清晰，更推荐使用`await`​​命令。

          ```javascript
          const main = document.querySelector('main');

          import(`./section-modules/${someVariable}.js`)
            .then(module => {
              module.loadPageInto(main);
            })
            .catch(err => {
              main.textContent = err.message;
            });
          ```

          ```javascript
          async function renderWidget() {
            const container = document.getElementById('widget');
            if (container !== null) {
              // 等同于
              // import("./widget").then(widget => {
              //   widget.render(container);
              // });
              const widget = await import('./widget.js');
              widget.render(container);
            }
          }

          renderWidget();
          ```
      2. ​`import()`​​函数与所加载的模块没有静态连接关系，这点也是与`import`​​语句不相同。`import()`​​类似于 Node.js 的`require()`​​方法，区别主要是前者是异步加载，后者是同步加载。
    * **导入命令使用位置：**

      `import()`​​函数可以用在任何地方，不仅仅是模块，非模块的脚本也可以使用。它是运行时执行，也就是说，什么时候运行到这一句，就会加载指定的模块。
    * **适用场合：**

      1. **按需加载**  
          ​`import()`​​可以在需要的时候，再加载某个模块。

          ```javascript
          button.addEventListener('click', event => {
            import('./dialogBox.js')
            .then(dialogBox => {
              dialogBox.open();
            })
            .catch(error => {
              /* Error handling */
            })
          });
          ```

          上面代码中，`import()`​​方法放在`click`​​事件的监听函数之中，只有用户点击了按钮，才会加载这个模块。
      2. **条件加载**  
          ​`import()`​​可以放在`if`​​代码块，根据不同的情况，加载不同的模块。

          ```javascript
          if (condition) {
            import('moduleA').then(...);
          } else {
            import('moduleB').then(...);
          }
          ```

          上面代码中，如果满足条件，就加载模块 A，否则加载模块 B。
      3. **动态的模块路径**  
          ​`import()`​​允许模块路径动态生成。

          ```javascript
          import(f())
          .then(...);
          ```

          上面代码中，根据函数`f`​​的返回结果，加载不同的模块。

#### ESM的转发

* **转发方法：**

  1. 先输入后输出一个模块

      ```javascript
      import { foo, bar } from 'my_module';
      export { foo, bar };
      ```
  2. 直接输出转发模块

      ```javascript
      export { foo, bar } from 'my_module';
      ```

      > 注意：`foo`​和`bar`​实际上并没有被导入当前模块，只是相当于对外转发了这两个接口，导致当前模块不能直接使用`foo`​和`bar`​。
      >
  3. 直接转发所有接口：

      ```javascript
      export * from 'my_module';
      ```
  4. 转发默认接口：

      ```javascript
      export { default } from 'foo';
      ```
* **转发接口重命名：**

  1. 具名接口重命名

      ```javascript
      export { foo as myFoo } from 'my_module';
      ```
  2. 默认接口改为具名接口

      ```javascript
      export { default as es6 } from './someModule';
      ```
  3. 具名接口改为默认接口

      ```javascript
      export { es6 as default } from './someModule';

      // 等同于
      import { es6 } from './someModule';
      export default es6;
      ```
  4. 所有接口指定一个重命名的转发对象：

      ```javascript
      export * as ns from "mod";

      // 等同于
      import * as ns from "mod";
      export {ns};
      ```

#### ESM和CommonJs模块的区别

讨论 Node.js 加载 ES6 模块之前，必须了解 ES6 模块与 CommonJS 模块完全不同。

它们有三个重大差异。

* CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。
* CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。
* CommonJS 模块的`require()`​是同步加载模块，ES6 模块的`import`​命令是异步加载，有一个独立的模块依赖的解析阶段。

#### ESM在浏览器中的加载方式

浏览器加载 ES6 模块，也使用`<script>`​标签，但是要加入`type="module"`​属性。

```html
<script type="module" src="./foo.js"></script>
```

上面代码在网页中插入一个模块`foo.js`​，由于`type`​属性设为`module`​，所以浏览器知道这是一个 ES6 模块。

浏览器对于带有`type="module"`​的`<script>`​，都是异步加载，不会造成堵塞浏览器，即等到整个页面渲染完，再执行模块脚本，等同于打开了`<script>`​标签的`defer`​属性。

```html
<script type="module" src="./foo.js"></script>
<!-- 等同于 -->
<script type="module" src="./foo.js" defer></script>
```

如果网页有多个`<script type="module">`​，它们会按照在页面出现的顺序依次执行。

​`<script>`​标签的`async`​属性也可以打开，这时只要加载完成，渲染引擎就会中断渲染立即执行。执行完成后，再恢复渲染。

```html
<script type="module" src="./foo.js" async></script>
```

一旦使用了`async`​属性，`<script type="module">`​就不会按照在页面出现的顺序执行，而是只要该模块加载完成，就执行该模块。

ES6 模块也允许内嵌在网页中，语法行为与加载外部脚本完全一致。

```html
<script type="module">
  import utils from "./utils.js";

  // other code
</script>
```

举例来说，jQuery 就支持模块加载。

```html
<script type="module">
  import $ from "./jquery/src/jquery.js";
  $('#message').text('Hi from jQuery!');
</script>
```

对于外部的模块脚本（上例是`foo.js`​），有几点需要注意。

* 代码是在模块作用域之中运行，而不是在全局作用域运行。模块内部的顶层变量，外部不可见。
* 模块脚本自动采用严格模式，不管有没有声明`use strict`​。
* 模块之中，可以使用`import`​命令加载其他模块（`.js`​后缀不可省略，需要提供绝对 URL 或相对 URL），也可以使用`export`​命令输出对外接口。
* 模块之中，顶层的`this`​关键字返回`undefined`​，而不是指向`window`​。也就是说，在模块顶层使用`this`​关键字，是无意义的。
* 同一个模块如果加载多次，将只执行一次。

下面是一个示例模块。

```javascript
import utils from 'https://example.com/js/utils.js';

const x = 1;

console.log(x === window.x); //false
console.log(this === undefined); // true
```

利用顶层的`this`​等于`undefined`​这个语法点，可以侦测当前代码是否在 ES6 模块之中。

```javascript
const isNotModuleScript = this !== undefined;
```

#### ESM在Node中的加载方式

从 Node.js v13.2 版本开始，Node.js 已经默认打开了 ES6 模块支持。

**Node模块新的加载规则：**

1. ​`.mjs`​文件总是以 ES6 模块加载

    > Node.js 遇到`.mjs`​文件，就认为它是 ES6 模块，默认启用严格模式，不必在每个模块文件顶部指定`"use strict"`​。
    >
2. ​`.cjs`​文件总是以 CommonJS 模块加载
3. ​`.js`​文件的加载取决于`package.json`​里面`type`​字段的设置

    1. 指定`type`​字段为`module`​，该项目的 JS 脚本，就被解释成 ES6 模块。
    2. 没有`type`​字段或指定`type`​字段为`commonjs`​，该项目的 JS 脚本，就被解释成 CommonJS 模块。
4. 在`package.json`​中新增`exports`​字段设置，指定模块加载入口文件。

    1. 优先级高于`main`​字段
    2. 子目录别名  
        ​`package.json`​文件的`exports`​字段可以指定脚本或子目录的别名，然后从别名加载目录或文件

        ```javascript
        // ./node_modules/es-module-package/package.json
        {
          "exports": {
            "./submodule": "./src/submodule.js"
          }
        }

        import submodule from 'es-module-package/submodule';
        // 加载 ./node_modules/es-module-package/src/submodule.js
        ```

        ```javascript
        // ./node_modules/es-module-package/package.json
        {
          "exports": {
            "./features/": "./src/features/"
          }
        }

        import feature from 'es-module-package/features/x.js';
        // 加载 ./node_modules/es-module-package/src/features/x.js
        ```
    3. main 的别名  
        ​`exports`​字段的别名如果是`.`​，就代表模块的主入口，优先级高于`main`​字段，并且可以直接简写成`exports`​字段的值。

        ```javascript
        {
          "exports": {
            ".": "./main.js"
          }
        }

        // 等同于
        {
          "exports": "./main.js"
        }
        ```

        由于`exports`​字段只有支持 ES6 的 Node.js 才认识，所以可以用来兼容旧版本的 Node.js。

        ```javascript
        {
          "main": "./main-legacy.cjs",
          "exports": {
            ".": "./main-modern.cjs"
          }
        }
        ```

        上面代码中，老版本的 Node.js （不支持 ES6 模块）的入口文件是`main-legacy.cjs`​，新版本的 Node.js 的入口文件是`main-modern.cjs`​。
    4. 条件加载  
        利用`.`​这个别名，可以为 ES6 模块和 CommonJS 指定不同的入口。

        ```javascript
        {
          "type": "module",
          "exports": {
            ".": {
              "require": "./main.cjs",
              "default": "./main.js"
            }
          }
        }
        ```

        上面代码中，别名`.`​的`require`​条件指定`require()`​命令的入口文件（即 CommonJS 的入口），`default`​条件指定其他情况的入口（即 ES6 的入口）。

        上面的写法可以简写如下。

        ```javascript
        {
          "exports": {
            "require": "./main.cjs",
            "default": "./main.js"
          }
        }
        ```

        注意，如果同时还有其他别名，就不能采用简写，否则会报错。

        ```javascript
        {
          // 报错
          "exports": {
            "./feature": "./lib/feature.js",
            "require": "./main.cjs",
            "default": "./main.js"
          }
        }
        ```
5. CommonJS 模块加载 ES6 模块  
    CommonJS 的`require()`​命令不能加载 ES6 模块，会报错，只能使用`import()`​这个方法加载。

    ```javascript
    (async () => {
      await import('./my-app.mjs');
    })();
    ```
6. ES6 模块加载 CommonJS 模块  
    ES6 模块的`import`​命令可以加载 CommonJS 模块，但是只能整体加载，不能只加载单一的输出项。

    ```javascript
    // 正确
    import packageMain from 'commonjs-package';

    // 报错
    import { method } from 'commonjs-package';
    ```

    这是因为 ES6 模块需要支持静态代码分析，而 CommonJS 模块的输出接口是`module.exports`​，是一个对象，无法被静态分析，所以只能整体加载。

    加载单一的输出项，可以写成下面这样。

    ```javascript
    import packageMain from 'commonjs-package';
    const { method } = packageMain;
    ```

    > 还有一种变通的加载方法，就是使用 Node.js 内置的`module.createRequire()`​方法。
    >
    > ```javascript
    > // cjs.cjs
    > module.exports = 'cjs';
    >
    > // esm.mjs
    > import { createRequire } from 'module';
    >
    > const require = createRequire(import.meta.url);
    >
    > const cjs = require('./cjs.cjs');
    > cjs === 'cjs'; // true
    > ```
    >
    > 上面代码中，ES6 模块通过`module.createRequire()`​方法可以加载 CommonJS 模块。但是，这种写法等于将 ES6 和 CommonJS 混在一起了，所以不建议使用。
    >
7. 同时支持两种格式的模块的方式

    1. 如果原始模块是 ES6 格式，那么需要给出一个整体输出接口，比如`export default obj`​，使得 CommonJS 可以用`import()`​进行加载。
    2. 如果原始模块是 CommonJS 格式，那么可以加一个包装层，先整体输入 CommonJS 模块，然后再根据需要输出具名接口，然后将这个文件的后缀名改为`.mjs`​，或者将它放在一个子目录，再在这个子目录里面放一个单独的`package.json`​文件，指明`{ type: "module" }`​。

        ```javascript
        import cjsModule from '../index.js';
        export const foo = cjsModule.foo;
        ```
    3. 在`package.json`​文件的`exports`​字段，指明两种格式模块各自的加载入口。

        ```javascript
        "exports"：{
          "require": "./index.js"，
          "import": "./esm/wrapper.js"
        }
        ```

        上面代码指定`require()`​和`import`​，加载该模块会自动切换到不一样的入口文件。
8. 内部变量使用限制  
    ES6 模块应该是通用的，同一个模块不用修改，就可以用在浏览器环境和服务器环境。为了达到这个目标，Node.js 规定 ES6 模块之中不能使用 CommonJS 模块的特有的一些内部变量。  
    首先，就是`this`​关键字。ES6 模块之中，顶层的`this`​指向`undefined`​；CommonJS 模块的顶层`this`​指向当前模块，这是两者的一个重大差异。  
    其次，以下这些顶层变量在 ES6 模块之中都是不存在的。

    * ​`arguments`​
    * ​`require`​
    * ​`module`​
    * ​`exports`​
    * ​`__filename`​
    * ​`__dirname`​

## Webpack与模块化系统

Webpack于2017年发布并使用自己的[Webpack Modules](https://webpack.docschina.org/concepts/modules/)，以支持使用不同模块化规范开发的项目通过Webpack进行项目构建，并在浏览器端统一使用Webpack模块系统运行打包后的代码，虽然在ESM发布后，不管是浏览器、Node.js还是NPM包生态的发展都在增加ESM的支持。

但在现阶段，我们还无法统一使用ESM进行开发，还存在着一些如下方的问题：

1. 我们无法在生产环境锚定用户会使用高版本浏览器。

    > 虽然像Vite，SnowPack一类打包工具己经可以基于在浏览器中运行ESM进行项目构建，但我们的项目出于风险考虑，往往不会在生产环境中直接使用。
    >
2. CommonJs长期作为Nodejs服务端JS标准且Nodejs前期对ESM标准推进的太慢，已在过去影响了大量基于CommonJs进行开发的项目及NPM包的生态。

    > 如今的NPM包发布往往会采用CommonJS与ESM双标准发布模式，但完全像Pure ESM迁移依然需要时间，Pure ESM发布包模式目前也处于争议阶段。
    >

ESM的开发所需的基础设施也还未完全成熟，使用像Webpack之类的构建工具帮助项目在模块化变革中的稳定运行、发展和处理模块化问题还是很有必要的。

> JS模块化开发会逐步成为基础设施，而上面的这些过渡阶段问题也终将成为历史，但新的问题和技术在未来还会不断出现，而前端工程问题也需要配套的工具来将解决方案推向极致。

> 参考链接：
>
> * [The Evolution of JavaScript Modularity](https://github.com/myshov/history-of-javascript/tree/master/4_evolution_of_js_modularity)
> * [Javascript语言的历史](https://javascript.ruanyifeng.com/introduction/history.html)
> * [前端开发的历史和趋势](https://github.com/ruanyf/jstraining/blob/master/docs/history.md)

# **后续文章内容**

后续文章中，我们将逐步介绍webpack的核心概念和开发必备技巧，帮助大家了解webpack的配置构建、优化策略、内部运行原理及编写自定义Loader和插件。

* 基础用法：常见概念，资源解析，文件压缩，文件指纹，热更新等
* 进阶用法：tree shaking，代码分割，Source Map等
* 优化用法：并行压缩，多实例构建，使用webpack缓存，公共资源分包，动态polyfill等
* 原理解析：源码了解webpack打包原理、编写Loader和Plugin

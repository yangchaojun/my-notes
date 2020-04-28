### 什么是babel

- Babel文档：https://babeljs.io/docs/en/

一句话介绍

> Babel 是一个`JS`编译器。

更完整的介绍

> Babel 是一个工具链，主要用于将`ECMAScript2015+`版本的代码转为向后兼容的`JavaScript`语法，以便能够运行在当前和旧版本的浏览器和其他环境（如Node)去。

`Babel`的作用：

- 语法转换
- 通过==Polyfill==方式在目标环境中添加缺失的特性`(@babel/polyfill模块)`
- 源码转换(codemods)

具体作用：

- 解析ES6+代码
- 识别JSX
- 识别Flow、TypeScript等类型注解并跳过

严格来说，babel 也可以转化为更低的规范。但以目前情况来说，es5 规范已经足以覆盖绝大部分浏览器，因此常规来说转到 es5 是一个安全且流行的做法

babel 总共分为三个阶段：解析，转换，生成。

### @babel/core是什么

@babel/core是babel的核心，本身不具有任何转换功能，babel将转换的功能分解到一个一个plugin中，因此当我们不配置任何插件时，即使经过babel编译之后的代码与之前的代码时相同的。（这是因为==Babel 6== 做了大量模块化的工作，将原来集成一体的各种编译功能分离出去，独立成插件）

### 插件

上面说到不安装相应的插件，babel的编译根本起不了作用。比如要将箭头函数编译成普通函数需要使用[@babel/plugin-transform-arrow-functions](https://babeljs.io/docs/en/babel-plugin-transform-arrow-functions)，要将const或者let变量编译成var变量需要[@babel/plugin-transform-block-scoping](https://babeljs.io/docs/en/babel-plugin-transform-block-scoping)，要将class关键字转化成传统基于原型的类需要[@babel/plugin-transform-classes](https://babeljs.io/docs/en/babel-plugin-transform-classes)，所以为了真正的编译我们是可能需要大量各种的插件的，具体插件有哪些请点[这里](https://babeljs.io/docs/en/plugins)

#### 使用插件会产生两个问题

- 第一个问题是在使用babel 调用插件采用的是命令行参数的形式,如上面的：--plugins @babel/plugin-transform-arrow-functions
   这种写法在单个插件的情况下还好，多个插件的使用就是噩梦了，所以为了解决第一种问题，出现了一个叫**.babelrc**的配置文件，我们可以将多个插件的信息写入到配置文件中，因为@babel/cli在调用之时都会去调用**.babelrc**文件，这样就不用写老长的命令行参数了。
   例如我们同样是编译这段代码：`let fun = () => console.log('hello babel')`
   我们在**.babelrc**文件中写上

```js
{
  "plugins": [
    "@babel/plugin-transform-arrow-functions"
    "@babel/plugin-transform-block-scoping" 
  ]
}
```

- 第二个问题是同样是关于多个插件的使用的，如果我的代码中大量使用插件，那我依然避免不了在配置文件中大量填写插件信息的工作，但是伟大的babel为了让程序员们有更多的时间做自己喜爱的事情，而不是浪费生命在一个一个的挑选插件，然后把它们写在.babelrc上，它提供了一个叫做**preset**的概念，说好听点叫**预设**，直白点就是**插件包**的意思，意味着babel会预先替我们做好了一系列的插件包，例如下面这些babel认为程序员会用到的常用的插件包:
  - [@babel/preset-env](https://babeljs.io/docs/en/babel-preset-env)
  - [@babel/preset-flow](https://babeljs.io/docs/en/babel-preset-flow)
  - [@babel/preset-react](https://babeljs.io/docs/en/babel-preset-react)
  - [@babel/preset-typescript](https://babeljs.io/docs/en/babel-preset-typescript)

### 预设

通过使用或创建一个 `preset` 即可**轻松**使用一组插件。

#### @babel/preset-env

`@babel/preset-env` 主要作用是对我们所使用的并且目标浏览器中缺失的功能进行代码转换和加载 `polyfill`，在不进行任何配置的情况下，`@babel/preset-env` 所包含的插件将支持所有最新的JS特性(ES2015,ES2016等，不包含 stage 阶段)，将其转换成ES5代码。例如，如果你的代码中使用了可选链(目前，仍在 stage 阶段)，那么只配置 `@babel/preset-env`，转换时会抛出错误，需要另外安装相应的插件。

```js
//.babelrc
{ "presets": ["@babel/preset-env"]}
```

#### @babel/polyfill

> babel 编译过程处理第一种情况 - 统一语法的形态，通常是高版本语法编译成低版本的，比如 ES6 语法编译成 ES5 或 ES3。而 babel-polyfill 处理第二种情况 - 让目标浏览器支持所有特性，不管它是全局的，还是原型的，或是其它。这样，通过 babel-polyfill，不同浏览器在特性支持上就站到同一起跑线。
>
> polyfill我们又称垫片，见名知意，所谓垫片也就是垫平不同浏览器或者不同环境下的差异，因为有的环境支持这个函数，有的环境不支持这种函数，解决的是有与没有的问题，这个是靠单纯的@babel/preset-env不能解决的，因为@babel/preset-env解决的是将高版本写法转化成低版本写法的问题，因为不同环境下低版本的写法有可能不同而已。

补充说明 (2020/01/07)：**V7.4.0 **版本开始，`@babel/polyfill` 已经被废弃(前端发展日新月异)，需单独安装 `core-js` 和 `regenerator-runtime` 模块。



### @babel/plugin-transform-runtime

`@babel/plugin-transform-runtime` 是一个可以重复使用 `Babel` 注入的帮助程序，以节省代码大小的插件。

### 参考

[不容错过的 Babel7 知识](https://juejin.im/post/5ddff3abe51d4502d56bd143)

[一口(很长的)气了解 babel](https://juejin.im/post/5c19c5e0e51d4502a232c1c6#heading-0)

[babel 7 的使用的个人理解](https://www.jianshu.com/p/cbd48919a0cc)
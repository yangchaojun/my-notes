---
typora-root-url: ..\images
---

### 1.自动清理构建目录产物

#### 通过 npm script 清理构建目录

- rm -rf ./dist && webpack
- rimraf ./dist && webpack （需要安装 rimraf）

#### 使用 clean-webpack-plugin

- 默认会删除 output 指定的输出目录

### 2.devtool 的作用

`devtool`中的一些设置，可以帮助开发者将编译后的代码映射回原始源代码，不同的值会明显影响到构建和重新构建的速度。

```js
//webpack.config.js
module.exports = {
	devtool: 'cheap-module-eval-source-map', //开发环境下使用
}
```

生产环境可以使用 `none` 或者是 `source-map`，使用 `source-map` 最终会单独打包出一个 `.map` 文件，我们可以根据报错信息和此 `map` 文件，进行错误解析，定位到源代码。

`source-map` 和 `hidden-source-map` 都会打包生成单独的 `.map` 文件，区别在于，`source-map` 会在打包出的 js 文件中增加一个引用注释，以便开发工具知道在哪里可以找到它。`hidden-source-map` 则不会在打包的 js 中增加引用注释。

但是我们一般不会直接将 `.map` 文件部署到 CDN，因为会直接映射到源码，更希望将`.map` 文件传到错误解析系统，然后根据上报的错误信息，直接解析到出错的源码位置。

[其他 devtool 的值](http://webpack.html.cn/configuration/devtool.html)

### 3.静态资源拷贝

[CopyWebpackPlugin](https://webpack.js.org/plugins/copy-webpack-plugin/)

```js
//webpack.config.js
const CopyWebpackPlugin = require('copy-webpack-plugin')
module.exports = {
	//...
	plugins: [
		new CopyWebpackPlugin(
			[
				{
					from: 'public/js/*.js',
					to: path.resolve(__dirname, 'dist', 'js'),
					flatten: true, //这里说一下 flatten 这个参数，设置为 true，那么它只会拷贝文件，而不会把文件夹路径都拷贝上，大家可以不设置 flatten 时，看下构建结果
				},
				//还可以继续配置其它要拷贝的文件
			],
			{
				ignore: ['other.js'], // 如果我们要拷贝一个目录下的很多文件，但是想过滤掉某个或某些文件，那么 CopyWebpackPlugin 还为我们提供了 ignore 参数
			}
		),
	],
}
```

### 4.ProvidePlugin

### 5.resolve 配置

`resolve`配置`webpack`如何寻找模块对应的文件。`webpack`内置`JavaScript`模块化语法解析功能，默认会采用模块化标准用约定好的规则去寻找，但开发者可以根据自己的需要修改默认规则。

#### 1.modules

`resolve.modules` 配置 `webpack` 去哪些目录下寻找第三方模块，默认情况下，只会去 `node_modules` 下寻找，如果你我们项目中某个文件夹下的模块经常被导入，不希望写很长的路径，那么就可以通过配置 `resolve.modules` 来简化。

```js
//webpack.config.js
module.exports = {
	//....
	resolve: {
		modules: ['./src/components', 'node_modules'], //从左到右依次查找
	},
}
```

这样配置之后，我们 `import Dialog from 'dialog'`，会去寻找 `./src/components/dialog`，不再需要使用相对路径导入。如果在 `./src/components` 下找不到的话，就会到 `node_modules` 下寻找。

#### 2.alias

`resolve.alias` 配置项通过别名把原导入路径映射成一个新的导入路径，例如：

```js
//webpack.config.js
module.exports = {
	//....
	resolve: {
		alias: {
			'react-native': '@my/react-native-web', //这个包名是我随便写的哈
		},
	},
}
```

例如，我们有一个依赖 `@my/react-native-web` 可以实现 `react-native` 转 `web`。我们代码一般下面这样:

```
import { View, ListView, StyleSheet, Animated } from 'react-native';
复制代码
```

配置了别名之后，在转 web 时，会从 `@my/react-native-web` 寻找对应的依赖。

当然啦，如果某个依赖的名字太长了，你也可以给它配置一个短一点的别名，这样用起来比较爽，尤其是带有 `scope` 的包。

#### 3.extensions

适配多端的项目中，可能会出现 `.web.js`, `.wx.js`，例如在转 web 的项目中，我们希望首先找 `.web.js`，如果没有，再找 `.js`。我们可以这样配置:

```js
//webpack.config.js
module.exports = {
	//....
	resolve: {
		extensions: ['web.js', '.js'], //当然，你还可以配置 .json, .css
	},
}
```

首先寻找 `../dialog.web.js` ，如果不存在的话，再寻找 `../dialog.js`。这在适配多端的代码中非常有用，否则，你就需要根据不同的平台去引入文件(以牺牲了速度为代价)。

```
import dialog from '../dialog';
```

当然，配置 `extensions`，我们就可以缺省文件后缀，在导入语句没带文件后缀时，会自动带上`extensions` 中配置的后缀后，去尝试访问文件是否存在，因此要将高频的后缀放在前面，并且数组不要太长，减少尝试次数。如果没有配置 `extensions`，默认只会找对应的 js 文件。

#### 4.enforceExtension

如果配置了 `resolve.enforceExtension` 为 `true`，那么导入语句不能缺省文件后缀。

#### 5. mainFields

有一些第三方模块会提供多份代码，例如 `bootstrap`，可以查看 `bootstrap` 的 `package.json` 文件：

```json
{
	"style": "dist/css/bootstrap.css",
	"sass": "scss/bootstrap.scss",
	"main": "dist/js/bootstrap"
}
```

`resolve.mainFields` 默认配置是 `['browser', 'main']`，即首先找对应依赖 `package.json` 中的 `brower` 字段，如果没有，找 `main` 字段。

如：`import 'bootstrap'` 默认情况下，找得是对应的依赖的 `package.json` 的 `main` 字段指定的文件，即 `dist/js/bootstrap`。

假设我们希望，`import 'bootsrap'` 默认去找 `css` 文件的话，可以配置 `resolve.mainFields` 为:

```js
//webpack.config.js
module.exports = {
	//....
	resolve: {
		mainFields: ['style', 'main'],
	},
}
```

### 6.区分不同的环境

---

目前为止我们 `webpack` 的配置，都定义在了 `webpack.config.js` 中，对于需要区分是开发环境还是生产环境的情况，我们根据 `process.env.NODE_ENV` 去进行了区分配置，但是配置文件中如果有多处需要区分环境的配置，这种显然不是一个好办法。

更好的做法是创建多个配置文件，如: `webpack.base.js`、`webpack.dev.js`、`webpack.prod.js`。

- `webpack.base.js` 定义公共的配置
- `webpack.dev.js`：定义开发环境的配置
- `webpack.prod.js`：定义生产环境的配置

[webpack-merge](https://www.npmjs.com/package/webpack-merge) 专为 `webpack` 设计，提供了一个 `merge` 函数，用于连接数组，合并对象。

安装依赖：

```bash
npm install webpack-merge -D
```

```js
const merge = require('webpack-merge');
merge({
    devtool: 'cheap-module-eval-source-map',
    module: {
        rules: [
            {a: 1}
        ]
    },
    plugins: [1,2,3]
}, {
    devtool: 'none',
    mode: "production",
    module: {
        rules: [
            {a: 2},
            {b: 1}
        ]
    },
    plugins: [4,5,6],
});
//合并后的结果为
{
    devtool: 'none',
    mode: "production",
    module: {
        rules: [
            {a: 1},
            {a: 2},
            {b: 1}
        ]
    },
    plugins: [1,2,3,4,5,6]
}
```

`webpack.config.base.js`中是通用的`webpack`配置，已`webpack.config.dev.js`为例：

```js
//webpack.config.dev.js
const merge = require('webpack-merge')
const baseWebpackConfig = require('./webpack.config.base')

module.exports = merge(baseWebpackConfig, {
	mode: 'development',
	//...其它的一些配置
})
```

然后修改我们的 `package.json`，指定对应的 `config` 文件：

```js
//package.json
{
    "scripts": {
        "dev": "cross-env NODE_ENV=development webpack-dev-server --config=webpack.config.dev.js",
        "build": "cross-env NODE_ENV=production webpack --config=webpack.config.prod.js"
    },
}
```

你可以使用 `merge` 合并，也可以使用 `merge.smart` 合并，`merge.smart` 在合并`loader`时，会将同一匹配规则的进行合并，`webpack-merge` 的说明文档中给出了详细的示例。

### 7.定义环境变量

很多时候，我们在开发环境中会使用预发环境或者是本地的域名，生产环境中使用线上域名，我们可以在 `webpack` 定义环境变量，然后在代码中使用。

使用 `webpack` 内置插件 `DefinePlugin` 来定义环境变量。

`DefinePlugin` 中的每个键，是一个标识符.

- 如果 `value` 是一个字符串，会被当做 `code` 片段
- 如果 `value` 不是一个字符串，会被`stringify`
- 如果 `value` 是一个对象，正常对象定义即可
- 如果 `key` 中有 `typeof`，它只针对 `typeof` 调用定义

```js
//webpack.config.dev.js
const webpack = require('webpack')
module.exports = {
	plugins: [
		new webpack.DefinePlugin({
			DEV: JSON.stringify('dev'), //字符串
			FLAG: 'true', //FLAG 是个布尔类型
		}),
	],
}

//index.js
if (DEV === 'dev') {
	//开发环境
} else {
	//生产环境
}
```

### 8.利用 webpack 解决跨域问题

假设前端在 3000 端口，服务端在 4000 端口，我们通过 `webpack` 配置的方式去实现跨域。

首先，我们在本地创建一个 `server.js`：

```js
let express = require('express')

let app = express()

app.get('/api/user', (req, res) => {
	res.json({ name: '杨超俊' })
})

app.listen(4000)
```

执行代码(`run code`)，现在我们可以在浏览器中访问到此接口: `http://localhost:4000/api/user`。

在 `index.js` 中请求 `/api/user`，修改 `index.js` 如下:

```js
//需要将 localhost:3000 转发到 localhost:4000（服务端） 端口
fetch('/api/user')
	.then((response) => response.json())
	.then((data) => console.log(data))
	.catch((err) => console.log(err))
```

我们希望通过配置代理的方式，去访问 4000 的接口。

#### 配置代理

修改 `webpack` 配置:

```js
//webpack.config.js
module.exports = {
	//...
	devServer: {
		proxy: {
			'/api': 'http://localhost:4000',
		},
	},
}
```

重新执行 `npm run dev`，可以看到控制台打印出来了 `{name: "杨超俊"}`，实现了跨域。

大多情况，后端提供的接口并不包含 `/api`，即：`/user`，`/info`、`/list` 等，配置代理时，我们不可能罗列出每一个 api。

修改我们的服务端代码，并重新执行。

```js
//server.js
let express = require('express')

let app = express()

app.get('/user', (req, res) => {
	res.json({ name: '杨超俊' })
})

app.listen(4000)
```

尽管后端的接口并不包含 `/api`，我们在请求后端接口时，仍然以 `/api` 开头，在配置代理时，去掉 `/api`，修改配置:

```js
//webpack.config.js
module.exports = {
	//...
	devServer: {
		proxy: {
			'/api': {
				target: 'http://localhost:4000',
				pathRewrite: {
					'/api': '',
				},
			},
		},
	},
}
```

重新执行 `npm run dev`，在浏览器中访问： `http://localhost:3000/`，控制台中也打印出了`{name: "杨超俊"}`，跨域成功，

### 9.PostCSS 插件 autoprefixer 自动补齐样式

安装依赖

```bash
npm i postcss-loader autoprefixer -D
```

```js
'use strict'
const path = require('path')
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const { CleanWebpackPlugin } = require('clean-webpack-plugin')
module.exports = {
	// 其他配置
	module: {
		rules: [
			{ test: /\.js$/, use: 'babel-loader' },
			{
				test: /\.css$/,
				use: [MiniCssExtractPlugin.loader, 'css-loader'],
			},
			{
				test: /\.less$/,
				use: [
					MiniCssExtractPlugin.loader,
					'css-loader',
					'less-loader',
					{
						loader: 'postcss-loader',
						options: {
							plugins: () => [require('autoprefixer')()],
						},
					},
				],
			},
		],
	},
}
```

### 10.移动端 CSS px 自动转换成 rem

使用 px2rem-loader

页面渲染时计算根元素的`font-size`值

- 可以使用手淘的 lib-flexible 库
- https://github.com/amfe/lib-flexible

```js
module.exports = {
	// 其他配置
	module: {
		rules: [
			{ test: /\.js$/, use: 'babel-loader' },
			{
				test: /\.css$/,
				use: [MiniCssExtractPlugin.loader, 'css-loader'],
			},
			{
				test: /\.less$/,
				use: [
					MiniCssExtractPlugin.loader,
					'css-loader',
					'less-loader',
					{
						loader: 'px2rem-loader',
						options: {
							remUnit: 75,
							remPrecision: 8, // 小数点位数
						},
					},
				],
			},
		],
	},
}
```

### 11.静态资源内联

#### 意义

代码层面：

- 页面框架的初始化脚本
- 上报相关打点
- css 内联便面页面闪动

请求层面：

- 小图片或者字体内联（url-loader)

#### JS 和 Html 的内联

安装依赖

```js
npm i raw-loader@0.5.1 -D
```

用法

```html
<!DOCTYPE html>
<html lang="en">
	<head>
		<%=require('raw-loader!./meta.html')%>
		<title>Index page</title>
	</head>
	<body></body>
</html>
```

#### CSS 内联

方案一：借助 style-loader

```
'use strict'
const path = require('path')
module.exports = {
	// ...
	module: {
		rules: [
			{ test: /\.js$/, use: 'babel-loader' },
			{ // style-loader 在css-loader之前
				test: /\.css$/,
				use: [{
					loader: 'style-loader',
					options: {
						insertAt: 'top', //样式插入到<head>
						singleon: true // 将所有的style标签合并
					}
				}, 'css-loader'],
			},
		],
	},
	mode: 'production',
}
```

方案二：html-inline-css-webpack-plugin

### 12.多页面应用打包同样方案

多页面应用（MPA)概念

每一次页面跳转的时候，后台服务器都会返回一个新的 html 文档，

这种类型的网站也就是多页网站，也叫多页应用。

#### 多页面打包基本方案

每个页面对应一个 entry,一个 html-webpack-plugin

缺点：每次新增或删除页面需要改 webpack 配置

#### 多页面打包通用方案

动态获取 entry 和设置 html-webpack-plugin 数量

利用 glob.sync

- entry：glob.sync(path.join(\_\_dirname, './src/\*/index.js'))

```js
// 核心方法
onst setPMA = () => {
	const entry = {}
	const htmlWebpackPlugins = []
	const entryFiles = glob.sync(path.join(__dirname, './src/*/index.js'))
	Object.keys(entryFiles).map((index) => {
		const entryFile = entryFiles[index]
		const match = entryFiles[index].match(/src\/(.*)\/index\.js/)
		const pageName = match && match[1]
		console.log(pageName)
		entry[pageName] = entryFile
		htmlWebpackPlugins.push(
			new HtmlWebpackPlugin({
				template: path.join(__dirname, `./src/${pageName}/index.html`),
				filename: `${pageName}.html`,
				chunks: [pageName],
				inject: true,
				minify: {
					html5: true,
					collapseWhitespace: true,
					preserveLineBreaks: false,
					minifyCSS: true,
					minifyJS: true,
					removeComments: false,
				},
			})
		)
	})
	return {
		entry,
		htmlWebpackPlugins,
	}
}
const { entry, htmlWebpackPlugins } = setPMA()
```

### 13.使用 source map

作用：通过 source map 定位到源代码

[科普](http://www.ruanyifeng.com/blog/2013/01/javascript_source_map.html)

开发环境开启，线上环境关闭

- 线上怕排查问题的时候可以将 sourcemap 上传到错误监控系统

#### source map 关键字

- eval：使用 eval 包裹模块代码
- source map：产生.map 文件
- cheap：不包括列信息
- inline：将.map 作为 DataURI 嵌入，不单独生成.map 文件
- module：包括 loader 的 sourcemap

#### source map 类型

![](.\批注 2020-04-29 223510.png)

```js

```

### 提取公共资源externals

#### 基础库分离

- 思路：将 react、react-dom 基础包通过 cdn 引入，不打入 bundle 中
- 方法：使用 html-webpack-externals-plugin

html 需引入 cdn

```js
	new HtmlWebpackExternalsPlugin({
			externals: [
				{
					module: 'react',
					entry:
						'https://cdn.bootcdn.net/ajax/libs/react/16.13.1/umd/react.production.min.js',
					global: 'React',
				},
				{
					module: 'react-dom',
					entry:
						'https://cdn.bootcdn.net/ajax/libs/react-dom/16.13.1/umd/react-dom.production.min.js',
					global: 'ReactDOM',
				},
			],
		}),
```

#### 利用 SplitChunksPlugin 进行公共脚本分离

Webpack4 内置的，替代 CommonsChunkPlugin 插件

chunks 参数说明：

- async 异步引入的库进行分离（默认）
- initial 同步引入的库进行分离
- all 所有引入的库进行分离（推荐）

利用 SplitChunksPlugin 分离基础包

test: 匹配出需要分离的包

```js
module.exports = {
	optimization: {
		splitChunks: {
			cacheGroups: {
				commons: {
					test: /(react)|(react-dom)/,
					name: 'vendors',
					chunks: 'all',
				},
			},
		},
	},
}
```

minChunks: 设置最小引用次数为 2 次

minSize: 分离的包体积的大小

```js
module.exports = {
	optimization: {
		splitChunks: {
			minSize: 0,
			cacheGroups: {
				commons: {
					name: 'commons',
					chunks: 'all',
					minChunks: 2,
				},
			},
		},
	},
}
```

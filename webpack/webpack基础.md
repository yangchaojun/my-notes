---
typora-root-url: ..\images
---

### 1.webpack安装

```bash
# 创建工程
mkdir my-project
cd my-project
npm init -y

# 安装webpack 和 webpack-cli
npm install webpack webpack-cli --save-dev

# 安装成功
./node_modules/.bin/webpack -v // 4.43.0
```

### 2.一个使用webpack的简单例子

```javascript
// webpack.config.js
'use strict'
const path = require('path')
module.exports = {
	entry: './src/index.js',
	output: {
		path: path.join(__dirname, 'dist'),
		filename: 'bundle.js',
	},
	mode: 'production',
}
```

使用webpack命令打包之后会在根目录下生成一个dist目录，dist里面有一个叫bundle.js的文件

### 3.通过npm script 运行webpack

```json
{
	"name": "webpack-study",
	"version": "1.0.0",
	"description": "",
	"main": "index.js",
	"scripts": {
		"build": "webpack"
	},
	"keywords": [],
	"author": "",
	"license": "ISC",
	"devDependencies": {
		"webpack": "^4.43.0",
		"webpack-cli": "^3.3.11"
	}
}
```

通过npm run build 运行构建，原理：模块局部安装会在`node_modules/.bin`目录创建软连接

### 4.核心概念之entry

依赖图的入口是entry

#### Entry的用法

- 单入口：entry是一个字符串

  ```javascript
  module.exports = {
      entry: './path/to/my/entry/file.js'
  }
  ```

- 多入口：entry是一个对象

   ```js
  module.exports = {
  	entry: {
  		app: './src/app.js',
  		adminApp: './src/adminApp.js'
  	}
  }
  ```

### 5.核心概念之output

output用来告诉webpack如何将编译后的文件输出到磁盘

#### Output 的用法

- 单入口配置

  ```js
  'use strict'
  const path = require('path')
  module.exports = {
  	entry: './src/index.js',
  	output: {
  		path: path.join(__dirname, 'dist'),
  		filename: 'bundle.js',
  	},
  	mode: 'production',
  }
  ```

  

- 多入口配置

  ```js
  'use strict'
  const path = require('path')
  module.exports = {
  	entry: {
  		app: './src/index.js',
  		search: './src/search.js',
  	},
  	output: {
  		path: path.join(__dirname, 'dist'),
  		filename: '[name].js', // 通过占位符确保文件名称的唯一
  	},
  	mode: 'production',
  }
  ```

### 5.核心概念之Loaders

webpack开箱即用只支持`js`和`JSON`两种文件类型，通过`Loaders`去支持其他文件类型并且把它们转换为有效的模块，并且可以添加到依赖图中。

`Loaders`本身是一个函数，接受源文件作为参数，返回转换的结果。

`loader`的执行顺序是从右向左执行的，也就是后面的`loader`先执行

#### Loaders的用法

```js
'use strict'
const path = require('path')
module.exports = {
	entry: {
		app: './src/index.js',
		search: './src/search.js',
	},
	output: {
		path: path.join(__dirname, 'dist'),
		filename: '[name].js',
	},
    // test 指定匹配规则
    // use 指定使用的loader名称
	module: {
		rules: [{ test: /\.txt$/, use: 'raw-loader' }],
	},
	mode: 'production',
}
```

#### 常见的loader

![](/批注 2020-04-22 215936.png)

### 7.核心概念之Plugins

插件用于bundle文件的优化，资源管理和环境变量的注入

在webpack构建流程中的**特定时机注入扩展逻辑**来改变构建结果或做你想要做的事情

作用于整个构建过程

#### Plugins的用法

```js
'use strict'
const path = require('path')
module.exports = {
	// ...
    // 放到plugins数组里
	plugins: [new HtmlWebpackPlugin({ template: './src/index.html' })],
	mode: 'production',
}

```

#### 常见的plugin

![](/批注 2020-04-22 220453.png)

### 8.核心概念之Mode

Mode用来指定当前的构建环境是：production、development He none。

设置mode可以使用webpack内置的函数，默认值为production

#### Mode的内置函数功能

![](/批注 2020-04-22 221447.png)

### 9.资源解析之解析ES6

使用babel-loader

babel的配置文件是：.babelrc

#### 安装所需依赖

```bash
npm i @babel/core @babel/preset-env babel-loader -D
```

#### 用法

```js
'use strict'
const path = require('path')
module.exports = {
	//...
	module: {
		rules: [{ test: /\.jsx?$/, use: 'babel-loader', exclude: /node_modules/ //排除 node_modules 目录 }],
	},
	mode: 'production',
}

```

#### 创建`.babelrc`文件

```json
{
	"presets": ["@babel/preset-env"]
}
```

### 10.资源解析之解析JSX

#### 安装所需依赖

```bash
npm i react react-dom -S
npm i @babel/preset-react -D
```

#### 创建`.babelrc`文件

```json
{
	"presets": ["@babel/preset-env", "@babel/preset-react"]
}
```

### 11.资源解析之解析CSS

css-loader 用于加载.css文件，并且转换成commonjs对象

style-loader将样式通过`<style>`标签插入到head中

#### 安装所需依赖

```bash
npm i style-loader css-loader -D
```

#### 用法

```js
'use strict'
const path = require('path')
module.exports = {
	// ...
	module: {
		rules: [
			{ test: /\.js$/, use: 'babel-loader' },
			{ // style-loader 在css-loader之前
				test: /\.css$/,
				use: ['style-loader', 'css-loader'],
			},
		],
	},
	mode: 'production',
}

```

### 12.资源解析之解析LESS

less-loader 用于将less转换为css

#### 安装所需依赖

```bash
npm i less-loader less -D
```

#### 用法

```js
'use strict'
const path = require('path')
module.exports = {
	// ...
	module: {
		rules: [
			{ test: /\.js$/, use: 'babel-loader' },
			{
				test: /\.css$/,
				use: ['style-loader', 'css-loader'],
			},
			{
				test: /\.less$/,
				use: ['style-loader', 'css-loader', 'less-loader'],
			},
		],
	},
	mode: 'production',
}
```

### 13.资源解析之解析图片与字体资源

file-loader 用于处理文件

url-loader 也可以处理图片和字体，可以设置较小资源自动base64

#### 安装所需依赖

```bash
npm i file-loader url-loader -D
```

#### 用法

```js
'use strict'
const path = require('path')
module.exports = {
	// ...
	module: {
		rules: [
			// ...
			{
				test: /\.(png|jpg|svg|gif)$/,
				use: [
					{
						loader: 'url-loader',
						options: {
							limit: 10240, // 10K以下base64转换
						},
					},
				],
			},
			{
				test: /\.(woff|woff2|eot|ttf|otf)$/,
				use: 'file-loader',
			},
		],
	},
	mode: 'production',
}

```

### 14.webpack中的文件监听

文件监听是在发现源码发生变化是，自动重新构建出新的输出文件。

webpack开启监听模式，有两种方式：

- 启动webpack命令时，带上`--watch`参数
- 在配置webpack.config.js中设置`watch:true`

缺陷就是每次需要手动刷新浏览器

#### 文件监听的原理分析

轮询判断文件的最后编辑时间是否变化

某个文件发生了变化，并不会立刻告诉监听者，而是先缓存起来，等`aggregateTimeout`

```js
// webpack.config.js
module.export = {
	// 默认false，也就是不开启
	watch: true,
	// 只有开启监听模式时，watchOptions才有意义
	watchOptions: {
		// 默认为空，不监听的文件或者文件夹，支持正则匹配
		ingored: /node_modules/，
		// 监听到变化发生后会等待300ms再去执行，默认300ms
		aggregateTimeout: 300,
		// 判断文件是否发生变化是通过不停询问系统指定文件有没有变化实现的，默认没秒问1000次
		poll: 1000
	}
}
```

### 15.热更新：webpack-dev-server

WDS不刷新浏览器

WDS不输出文件，而是放在内存中

使用HotModuleReplacementPlugin插件

#### 安装依赖

```bash
npm i webpack-dev-server -D
```

#### 用法

```js
'use strict'
const path = require('path')
const webpack = require('webpack')
module.exports = {
	// ...
	mode: 'development',
	plugins: [new webpack.HotModuleReplacementPlugin()],
	devServer: {
		contentBase: './dist',
		hot: true,
	},
}

```

### 16.热更新：使用webpack-dev-middleware

WDM将webpack输出的文件传输给服务器

适用于灵活的定制场景

```js
const express = require('express')
const webpack = require('webpack')
const webpackDevMiddleware = require('webpack-dev-middleware')

const app = express()
const config = require('./webpack.config.js')
const complier = webpack(config)

app.use(
	webpackDevMiddleware(complier, {
		publicPath: config.output.publicPath,
	})
)

app.listen(3000, function () {
	console.log('Example app listening on port 3000\n')
})

```

#### 热更新原理

![批注 2020-04-23 211644](/批注 2020-04-23 211644.png)

### 17.文件指纹策略：chunkhash、contenthash和hash

文件指纹就是打包后输出的文件名的后缀

文件指纹如何生成

![](/批注 2020-04-23 213013.png)

#### JS的文件指纹设置

设置output的filename，使用`[chunkname]`

#### CSS的文件指纹设置

使用MiniCssExtractPlugin提取css文件

设置MiniCssExtractPlugin的filename，使用`[contenthash]`

使用MiniCssExtractPlugin.loader替换style-loader

#### 图片和字体的文件指纹设置

![](/批注 2020-04-23 220943.png)

文件指纹的设置一般用于生产环境

```js
'use strict'
const path = require('path')
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
module.exports = {
	entry: {
		app: './src/index.js',
		search: './src/search.js',
	},
	output: {
		path: path.join(__dirname, 'dist'),
		filename: '[name]_[chunkhash:8].js',
	},
	module: {
		rules: [
			{ test: /\.js$/, use: 'babel-loader' },
			{
				test: /\.css$/,
				use: [MiniCssExtractPlugin.loader, 'css-loader'],
			},
			{
				test: /\.less$/,
				use: [MiniCssExtractPlugin.loader, 'css-loader', 'less-loader'],
			},
			{
				test: /\.(png|jpg|svg|gif)$/,
				use: [
					{
						loader: 'file-loader',
						options: {
							name: 'img/[name]_[hash:8].[ext]',
						},
					},
				],
			},
			{
				test: /\.(woff|woff2|eot|ttf|otf)$/,
				use: [
					{
						loader: 'file-loader',
						options: {
							name: '[name]_[hash:8].[ext]',
						},
					},
				],
			},
		],
	},
	mode: 'production',
	plugins: [
		new MiniCssExtractPlugin({
			filename: `[name]_[contenthash:8].css`,
		}),
	],
}

```

### 18.文件压缩

#### JS文件压缩

webpack4 production下内置了`uglifyjs-webpack-plugin`

#### CSS 文件的压缩

使用`optimise-css-assets-webpack-plugin`

同时使用`cssnano`预处理器

```js
'use strict'
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin')
module.exports = {
	// ...
	mode: 'production',
	plugins: [
		new MiniCssExtractPlugin({
			filename: `[name]_[contenthash:8].css`,
		}),
		new OptimizeCssAssetsWebpackPlugin({
			assetNameRegExp: /\.css$/g,
			cssProcessor: require('cssnano'),
		}),
	],
}

```

#### html文件的压缩

修改html-webpack-plugin，设置压缩参数

```js
'use strict'
const path = require('path')
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin')
const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
	entry: {
		app: './src/index.js',
		search: './src/search.js',
	},
	output: {
		path: path.join(__dirname, 'dist'),
		filename: '[name]_[chunkhash:8].js',
	},
 	// ...
	mode: 'production',
	plugins: [
	 	// ...
        // 多个html，需配置对应HtmlWebpackPlugin
		new HtmlWebpackPlugin({
			template: path.join(__dirname, 'src/search.html'),
			filename: 'search.html',
			chunks: ['search'],
			inject: true,
			minify: {
				html5: true,
				collapseWhitespace: true, // 是否折叠空白
				preserveLineBreaks: false,
				minifyCSS: true,
				minifyJS: true,
				removeComments: false,
                removeAttributeQuotes: false, //是否删除属性的双引号
			},
		}),
		new HtmlWebpackPlugin({
			template: path.join(__dirname, 'src/index.html'),
			filename: 'index.html',
			chunks: ['app'],
			inject: true,
			minify: {
				html5: true,
				collapseWhitespace: true,
				preserveLineBreaks: false,
				minifyCSS: true,
				minifyJS: true,
				removeComments: false,
			},
		}),
	],
}
```


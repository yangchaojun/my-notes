### 1.自动清理构建目录产物

#### 通过npm script 清理构建目录

- rm -rf ./dist && webpack
- rimraf ./dist && webpack （需要安装rimraf）

#### 使用clean-webpack-plugin

- 默认会删除output指定的输出目录

### 2.PostCSS插件autoprefixer自动补齐样式

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
			}
		],
	}
}
```

#### 移动端 CSS px 自动转换成 rem

使用 px2rem-loader

页面渲染时计算根元素的`font-size`值

- 可以使用手淘的lib-flexible库
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
                            remPrecision: 8 // 小数点位数
                        }
					},
				],
			}
		],
	}
}
```

### 静态资源内联

#### 意义

代码层面：

- 页面框架的初始化脚本
- 上报相关打点
- css内联便面页面闪动

请求层面：

- 小图片或者字体内联（url-loader)

#### JS和Html的内联

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

#### CSS内联

方案一：借助style-loader

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

### 多页面应用打包同样方案

多页面应用（MPA)概念

每一次页面跳转的时候，后台服务器都会返回一个新的html文档，

这种类型的网站也就是多页网站，也叫多页应用。

#### 多页面打包基本方案

每个页面对应一个entry,一个html-webpack-plugin

缺点：每次新增或删除页面需要改webpack配置

#### 多页面打包通用方案

动态获取entry和设置html-webpack-plugin数量

利用glob.sync

- entry：glob.sync(path.join(__dirname, './src/*/index.js'))

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

### 使用source map

作用：通过 source map定位到源代码

[科普](http://www.ruanyifeng.com/blog/2013/01/javascript_source_map.html)

开发环境开启，线上环境关闭

- 线上怕排查问题的时候可以将sourcemap上传到错误监控系统

#### source map 关键字

- eval：使用eval包裹模块代码
- source map：产生.map文件
- cheap：不包括列信息
- inline：将.map作为DataURI嵌入，不单独生成.map文件
- module：包括loader的sourcemap

#### source map 类型

![](C:\Users\yangchaojun\Desktop\my-notes\images\批注 2020-04-29 223510.png)

```js

```


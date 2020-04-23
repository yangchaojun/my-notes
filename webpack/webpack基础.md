---
typora-root-url: ..\images
---

### webpack安装

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

### 一个使用webpack的简单例子

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

### 通过npm script 运行webpack

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

### 核心概念之entry

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

### 核心概念之output

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

### 核心概念之Loaders

webpack开箱即用只支持`js`和`JSON`两种文件类型，通过`Loaders`去支持其他文件类型并且把它们转换为有效的模块，并且可以添加到依赖图中。

`Loaders`本身是一个函数，接受源文件作为参数，返回转换的结果。

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

### 核心概念之Plugins

插件用于bundle文件的优化，资源管理和环境变量的注入

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

### 核心概念之Mode

Mode用来指定当前的构建环境是：production、development He none。

设置mode可以使用webpack内置的函数，默认值为production

#### Mode的内置函数功能

![](/批注 2020-04-22 221447.png)

### 资源解析之解析ES6

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
		rules: [{ test: /\.js$/, use: 'babel-loader' }],
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

### 资源解析之解析JSX

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

### 资源解析之解析CSS

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

#### 资源解析之解析LESS

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

#### 资源解析之解析图片与字体资源

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

#### 
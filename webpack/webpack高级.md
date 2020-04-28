### 1.自动清理构建目录产物

#### 通过npm script 清理构建目录

- rm -rf ./dist && webpack
- rimraf ./dist && webpack （需要安装rimraf）

#### 使用clean-webpack-plugin

- 默认会删除output指定的输出目录


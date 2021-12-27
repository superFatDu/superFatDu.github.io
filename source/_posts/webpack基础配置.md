---
title: webpack基础配置
date: 2021-1-24 22:15:28
tags: webpack
categories: 前端
comment: true
---

# 准备工作
 1. 初始化项目
 
```bash
$ npm init -y
```
 2. nvm,node,npm环境确认
 
```bash
$ nmv install/use version
$ node -v
$ npm -v
```
 3. webpack安装与使用
 
```bash
$ npm install -g/-D webpack webpack-cli //安装

$ npx webpack // 使用
$ node_modules/.bin/webpack // 或者
$ npm run script // 或者package.json配置脚本
```


# 基础配置（webpack.config.js)

```javascript
const path = require('path')
const nodeExternals = require('webpack-node-externals')
const { CleanWebpackPlugin } = require('clean-webpack-plugin')

const webpackConfig = {
  target: 'node',
  mode: 'development',
  entry: {
    server: path.join(__dirname, './src/index.js')
  },
  output: {
    filename: '[name].bundle.js',
    path: path.join(__dirname, './dist')
  },
  devtool: 'eval-source-map',
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        use: {
          loader: 'babel-loader'
        },
        exclude: [path.join(__dirname, './node_modules')]
      }
    ]
  },
  externals: [nodeExternals()],
  plugins: [
    new CleanWebpackPlugin()
  ],
  node: {
    // console: true,
    global: true,
    // process: true,
    // Buffer: true,
    __filename: true,
    __dirname: true,
    // setImmediate: true,
    // path: true
  }
}

module.exports = webpackConfig
```

# 热更新

```bash
// 1.安装nodemon
yarn add nodemon

// 2.配置脚本
nodemon src/index.js
```

# 支持ES6

```bash
// 1.安装babel等
yarn add @babel/core @babel/node @babel/preset-env babel-loader

// 2.添加.babelrc文件
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": {
          "node": "current"
        }
      }
    ]
  ]
}

// 3.配置脚本
nodemon --exec babel-node ./src/index.js
```

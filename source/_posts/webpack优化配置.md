---
title: webpack优化配置
date: 2021-1-25 23:44:12
tags: webpack
categories: 前端
comment: true
---

通常情况下，webpack的配置需要区分为开发环境和生产环境，我们把公共的部分抽离出来，再拼接成不同环境的配置。

```bash
$ tree
└── config
    ├── utils.js                    # 路径处理
    ├── webpack.config.base.js      # 基础配饰
    ├── webpack.config.prod.js      # 生产配置
    └── webpack.config.dev.js       # 开发配置
```

## webpack-merge

合并两个配置文件

```bash
// 1. 安装
$ yarn add webpack-merge

// 2.使用
const { merge } = require('webpack-merge')

```

## webpack.config.base.js

1. 使用webpack.DefinePlugin定义一个global constant：process.env
2. 去除不需要的一些配置项

```JavaScript
const path = require('path')
const webpack = require('webpack')
const nodeExternals = require('webpack-node-externals')
const { CleanWebpackPlugin } = require('clean-webpack-plugin')
const utils = require('./utils.js')

const webpackConfig = {
  target: 'node',
  // mode: 'development',
  entry: {
    // server: path.join(__dirname, './src/index.js')
    server: path.join(utils.APP_PATH, 'index.js')
  },
  output: {
    filename: '[name].bundle.js',
    // path: path.join(__dirname, './dist')
    path: utils.DIST_PATH
  },
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
    new CleanWebpackPlugin(),
    new webpack.DefinePlugin({
      'process.env': {
        NODE_ENV: (process.env.NODE_ENV === 'production' || process.env.NODE_ENV === 'prod') ? "'production'" : "'development'"
      }
    })
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

## webpack.config.dev.js


```javascript
const { merge } = require('webpack-merge')
const baseWebpackConfig = require('./webpack.config.base.js')

const webpackConfig = merge(baseWebpackConfig, {
  mode: 'development',
  devtool: 'eval-source-map',
  stats: { children: false }
})

module.exports = webpackConfig
```

## webpack.config.prod.js

1. 需要压缩代码等，安装terser-webpack-plugin
2. splitChunks分割代码

```javascript
const { merge } = require('webpack-merge')
const baseWebpackConfig = require('./webpack.config.base.js')
const TerserWebpackPlugin = require('terser-webpack-plugin')

const webpackConfig = merge(baseWebpackConfig, {
  mode: 'production',
  stats: { children: false, warnings: false },
  optimization: {
    minimize: true,
    minimizer: [
      new TerserWebpackPlugin({
        terserOptions: {
          warnings: false,
          compress: {
            warnings: false,
            drop_console: true,
            dead_code: true,
            drop_debugger: true
          },
          format: {
            comments: false,
          },
          mangle: true
        },
        parallel: true
      })
    ],
    splitChunks: {
      cacheGroups: {
        commons: {
          name: 'commons',
          chunks: 'initial',
          minChunks: 3,
          enforce: true
        }
      }
    }
  }
})

module.exports = webpackConfig
```

## 处理路径的utils.js

```JavaScript
const path = require('path')

exports.resolve = function resolve (dir) {
  return path.join(__dirname, '..', dir)
}

exports.APP_PATH = exports.resolve('src')
exports.DIST_PATH = exports.resolve('dist')
```

## 运行脚本

```bash
// 1. 安装cross-env
$ yarn add cross-env

// 2. package.json
"build": "cross-env NODE_ENV=production webpack --config config/webpack.config.prod.js"

// 3. 运行
$ yarn build
```

## 删除dist

```bash
// 1. 安装rimraf
$ yarn add rimraf -D

// 2. package.json
"clean": "rimraf dist"

```
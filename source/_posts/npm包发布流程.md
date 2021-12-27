---
title: npm包发布流程
date: 2020-12-22 15:01:04
tags: npm
categories: 前端
comment: true
---

## 1. 初始化

```bash
$ mkdir npmTest

$ cd .\npmTest\

$ npm init
```

## 2. 一步一步选择，形成package.json文件

```javascript
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.

See `npm help json` for definitive documentation on these fields
and exactly what they do.

Use `npm install <pkg>` afterwards to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
package name: (npmtest)
version: (1.0.0)
description: a test npm module
entry point: (index.js)
test command:
git repository:
keywords: a console function
author: mutu
license: (ISC)
About to write to C:\Users\super\Desktop\npmTest\package.json:

{
  "name": "npmtest",
  "version": "1.0.0",
  "description": "a test npm module",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [
    "a",
    "console",
    "function"
  ],
  "author": "mutu",
  "license": "ISC"
}


Is this OK? (yes) yes
```

## 3. 入口文件

```javascript
// index.js
function consoleFunc(arg) {
    console.log(arg)
}

module.exports = consoleFunc
```

## 4. 将当前镜像设置为官方镜像

```bash
$ npm set registry https://registry.npmjs.org/
```

## 5. 登录（如果npm没有账户首先注册用户，当前默认已有账户）

```bash
// 添加用户
$ npm adduser

$ Username: ch***5
$ Password: ******
$ Email: (this IS public) superdu***u@163.com

Logged in as cheng5 on https://registry.npmjs.org/.

// 查看当前用户
$ npm whoami
ch***5
```

## 6. 发布

```bash
$ npm publish

npm notice
npm notice package: mutu-npmtest@1.0.0
npm notice === Tarball Contents ===
npm notice 291B package.json
npm notice 83B  index.js
npm notice === Tarball Details ===
npm notice name:          mutu-npmtest
npm notice version:       1.0.0
npm notice package size:  364 B
npm notice unpacked size: 374 B
npm notice shasum:        62e8dccbdadb48bb753700b17cc98397003eb1ae
npm notice integrity:     sha512-ZQEudq/lPpf4F[...]/Rh8IBGGh/TkA==
npm notice total files:   2
npm notice
+ mutu-npmtest@1.0.0
```

## 7. 使用

```bash
// 1. 新建一个项目

// 2. 添加依赖包
$ yarn add mutu-npmtest

// 3. 使用
const cf = requier('mutu-npmtest')
cf('Hell0,I\'m ch***5')

// 4. 运行
$ node index.js
Hell0,I'm ch***5
```

## 8. 问题

```bash
npm ERR! publish Failed PUT 403
npm ERR! code E403
npm ERR! You do not have permission to publish "npmtest". Are you logged in as the correct user? : npmtest

npm ERR! A complete log of this run can be found in:
npm ERR!     C:\Users\super\AppData\Roaming\npm-cache\_logs\2020-11-23T08_09_30_485Z-debug.log
```
如上报错说明package.json中的name选项已经被使用，经修改为mutu-npmtest后成功发布。

## 9. 淘宝源


```bash
$ npm install cnpm -g --registry=https://registry.npm.taobao.org/
```

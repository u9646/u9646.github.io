---
title: 如何迈向TypeScript - 工程配置篇
date: 2020-05-06 13:27:55
tags:
 - typescript
---

> 本文主要介绍如何将你现有的项目平滑的迁移至TypeScript。

我们这里以React + Webpack + Babel项目为例。其他类似的项目其实也都大同小异，可以参考进行配置。

### 第一步：支持编译.ts, .tsx文件

首先我们安装相关npm包：
`npm i typescript @types/react @types/react-dom -D`

然后使用`tsc --init`命令生成`tsconfig.json`文件。

我们将`tsconfig.json`里面的:
```
{
  "jsx": "react", // 改为react
  "noEmit": true,
}
```

接下来我们再安装`npm i @babel/preset-typescript -D`

然后修改`babel`配置文件，增加这个preset:
```
{
  "presets": [
    "@babel/preset-typescript"
  ],
}
```

同时我们还需要调整一下`webpack.config.js`文件：
```
{
  resolve: {
    extensions: ['.js', '.jsx', '.ts', '.tsx']  // 增加.ts, .tsx
  },
  module: {
    rules: [
      {
        test: /\.(jsx|tsx)?$/, // 支持ts, tsx
        use: ['babel-loader'],
      }
    ]
  }
}
```

到这一步，我们已经可以将.ts, .tsx文件转换为.js, .jsx文件了，但是是没有ts校验的。

### 第二步：增加类型校验

我们需要通过`tsc`进行校验

在`package.json`里面我们增加一个校验脚本：
```
{
  "scripts": {
    "ts-check": "tsc --watch"
  }
}
```

这样，执行`npm run ts-check`的时候，就可以对.ts, .tsx类型文件进行校验了。

这个时候，即使ts-check有报错，也不会影响项目本身的构建。

这样，你就可以实现项目中新增内容的ts校验了。


如果你想更进一步，把原有的代码也改造成ts，需要怎么做呢，很简单，将.js, .jsx的文件改成.ts, .tsx类型即可。
然后根据ts的报错去修改的代码。

### 第三步：全面改造

我们可以将所有.js, .jsx文件都改成.ts, .tsx后缀，但是对于一个大的项目来说，可能会产生大量的报错，需要花费很大精力去修改，建议是按模块来分步骤改造。

如果你想批量处理.js, .jsx文件，这里可以使用`shelljs`批量修改文件后缀名

首先我们安装相关npm包：
```
npm i shelljs @types/shelljs ts-node -D
```

然后新建一个renameJS.ts文件

```
// renameJS.ts
import * as shelljs from 'shelljs'
shelljs.find('src')
.filter(file => file.match(/\.jsx?$/))
.forEach(file => {
  const newName = file.replace(/.j(sx?$)/, '.t$1')
  shelljs.mv(file, newname);
})

// package.json 增加script
{
  "rename-js": "ts-node renameJS.ts",
}

// 最后执行npm run rename-js即可
```

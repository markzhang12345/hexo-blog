---
title: "[NodeJS]Node.js基础"
date: 2024-03-20 18:45:43
tags: [web,NodeJS]
categories: 笔记
toc: true
cover: https://pplware.sapo.pt/wp-content/uploads/2019/10/nodejs_capa.jpg
---
# 模块
在Node.js中一般将代码合理拆分到不同的文件中，每一个文件就是一个模块，文件路径就是模块名。

在编写每个模块时，都有`require`、`exports`、`module`三个预先定义好的变量可供使用。

## require
`require`**函数**用于在当前模块中加载别的模块，传入一个模块名，即可返回一个模块作为到处对象。相对路径(`./`开头)和绝对路径(`/`开头)均可以作为模块名。

模块名中的.js可以省略。

```
let foo1 = require('./foo');
let foo2 = require('./foo.js');
let foo3 = require('/home/user/foo');
let foo4 = require('/home/user/foo.js');

// foo1至foo4中保存的是同一个模块的导出对象。
```

也可以使用`require `来加载JSON文件。

```
let data = require('./data.json')
```

## exports
`exports`**对象**时当前模块所导出的对象，用来导出公有方法和属性。`require`**函数**载入的就是该模块的`exports`**对象**。

```
exports.hello = function () {
    console.log('Hello World!');
};
```

## module
通过`module`**对象**可以访问当前对象的相关信息，但是其一般用来替换当前模块的导出对象。例如，一模块导出了一个对象，可以将其替换成一个函数：

```
module.exports = function () {
    console.log('Hello World!');
};
```

在以上代码中，模块的默认导出被替换成了一个函数。

## 模块初始化
若一个模块在一段JS代码中被反复的`require`,其只在第一次载入时进行一次初始化。之后无论导入出的对象或者函数名是否变化，其中的变量不再进行初始化。

### 二进制模块(暂时不重要)
虽然一般我们使用JS编写模块，但NodeJS也支持使用C/C++编写二进制模块。编译好的二进制模块除了文件扩展名是.node外，和JS模块的使用方式相同。虽然二进制模块能使用操作系统提供的所有功能，拥有无限的潜能，但对于前端同学而言编写过于困难，并且难以跨平台使用，因此不在本教程的覆盖范围内。

# 链接
学习链接：[七天学会NodeJS](https://nqdeng.github.io/7-days-nodejs/#1.5)

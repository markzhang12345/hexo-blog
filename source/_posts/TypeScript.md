---
title: "初识TypeScript"
date: 2024-03-27 13:46:47
tags: [web,TypeScript]
categories: 笔记
cover: https://cdn.thenewstack.io/media/2022/01/10b88c68-typescript-logo.png
---
# 关于TypeScript
TypeScript是JavaScript的一个超集，就像C++是C的超集一样。其支持ES6标准。

TypeScript由微软开发的自由和开源的编程语言。

TypeScript 设计目标是开发大型应用，它可以编译成纯JavaScript，编译出来的JavaScript可以运行在任何浏览器上。

# 语言特性
TypeScript是一种给JavaScript添加特性的语言扩展。其增加的功能包括：
- 类型批注和编译时类型检查
- 类型推断
- 类型擦除
- 接口
- 枚举
- Mixin
- 泛型编译
- 命名空间
- 元组
- Await

# JavaScript和TypeScript的区别
现有的JavaScript代码可以与TypeScript一并工作时无需任何修改，TypeScript只是通过类型注解提供编译时的静态类型检查。TypeScript可处理已有的JavaScript代码，并只对其中的TypeScript代码进行编译。

# TypeScript安装
推荐使用npm(pnpm)安装TypeScript环境。

全局安装：
```
pnpm install -g typescript
```
安装后即可使用`tsc`命令

本地安装：
```
pnpm install typescript --save-dev  //本地安装开发时依赖
```

安装后使用`npx tsc`命令，(`npx`会在项目中自动查找本地安装的TypeScript，并执行编译)。
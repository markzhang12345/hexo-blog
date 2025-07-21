---
title: "[NodeJS]代码的组织和部署"
date: 2024-03-21 19:38:23
tags: [web,NodeJS]
categories: 笔记
toc: true
cover: https://pplware.sapo.pt/wp-content/uploads/2019/10/nodejs_capa.jpg
---
使用NodeJS编写程序，需要对代码的目录结构和部署方式有个提前的了解，在编写代码时才能做到心中有数。好比修建房子，要先将脚手架搭好。
# 模块路径规则解析
在*NodeJS基础*中，我们知道`require`中的模块路径有相对路径（/开头）和绝对路径（盘符开头）两种写法，在时若一个模块的位置改变，其他的模块代码也需要调整，极不方便。因此，这里有第三种路径形式。
1.**内置模块**
    若传入的模块时内置模块，则直接传入模块名称，无需路径。
    ```
    require('fs');
    ```
2.**node_modules目录**
    NodeJS定义了一个特殊的`node_modules`目录用于存放模块。例如某个模块的绝对路径是`/home/user/hello.js`，在该模块中使用`require('foo/bar')`方式加载模块时，则NodeJS依次尝试使用以下路径。
    ```
    /home/user/node_modules/foo/bar
    /home/node_modules/foo/bar
    /node_modules/foo/bar
    ```
3.**NODE_PATH环境变量**
    与PATH环境变量类似，NodeJS允许通过NODE_PATH环境变量来指定额外的模块搜索路径。
# 包
模块的基本单位是单个的JS文件，默写复杂的模块可能由多个子模块组成。我们将多个子模块组成的大模块叫做**包**。

一个包中，需要一个入口模块来进行该模块的导入，入口模块的到处对象就是包的导出对象。例如由包为`cat`：
```
-/home/user/lib/
    -cat/
        head.js
        body.js
        main.js
```
这个包由三个子模块，`main.js`作为入口模块：
```
let head = require('./head');
let body = require('./body');

exports.creats = funtion(name) {
    return {
        name: name,
        head: head.creats(),
        body: body.creats()
    };
};
```
在其他模块使用它时，加载`main.js`，即`require('/home/user/lib/cat/main')`。然而，入口模块的名字(`main.js`)出现在路径中显然不具有良好的可读性。
## index.js
若某个模块的名称时`index.js`，在加载模块时路径中可省略该模块名，即`index.js`作为`cat`的入口模块时，下面两个语句等价：
```
let cat = require('/home/user/lib/cat);
let cat = require('/home/user/lib/cat/index);
```
## package.json
开发者可以自定义入口米快的文件名和存放位置，在`pacjage.json`中指定即可：
```
{
    "name": "cat",
    "main": "./lib/main.js"
}

```
这样，使用`require('/home/user/lib/cat')`，NodeJS一样可以根据`package,json`中的信息找到入口模块的位置。
# 命令行程序
使用NodeJS编写的东西，要么是包，要么是命令行程序，前者的目的也是用于后者的开发。

因此，我们在部署代码时需要使用一些技巧来帮助用户觉得自己在使用一个命令行程序。

例如我们写了个程序，可以输出你在命令行输入的话。我们在主模块实现了所有功能，并将模块部署在`/home/user/bin/node-echo.js`这个位置。如果我们要在任何目录下使用这个程序，我们需要使用以下终端命令：
```
$ node /home/user/bin/node-echo.js Hello World
Hello World
```
这种方式麻烦，而且不像一个命令行程序。我们期望的使用方式如下：
```
$ node-echo Hello World
```

这里pnpm,vite,hexo等包都是以命令行程序的形式呈现的。
## Linux
将JS文件当作shell脚本来运行，从而达到上述目的。(不会，以后再看😟)

## windows
可以使用`.cmd`文件解决问题。我们需要将`C:\User\user\bin`*(假设`node-echo.js`存放在`C:\Users\user\bin`目录)添加至环境变量，再在该目录下创建一个`node-echo.cmd`的文件，内容如下：
```
@node "C:\User\user\bin\node-echo.js" %*
```
目的达成。

# 工程目录
以编写一个命令行程序为例，我们一般要提供命令行模式和API模式两种使用方式，并使用第三方包来编写代码。

除了代码，一个完整的程序应该有自己的文档和测试用例。所以，一个完整的工程文件应该看起来像下面这样：
```
- /home/user/workspace/node-echo/   # 工程目录
    - bin/                          # 存放命令行相关代码
        node-echo
    + doc/                          # 存放文档
    - lib/                          # 存放API相关代码
        echo.js
    - node_modules/                 # 存放三方包
        + argv/
    + tests/                        # 存放测试用例
    package.json                    # 元数据文件
    README.md                       # 说明文件
```
其中部分文件如下：
```
/* bin/node-echo */
let argv = require('argv'),
    echo = require('../lib/echo');
console.log(echo(argv.join(' ')));

/* lib/echo.js */
module.exports = function (message) {
    return message;
};

/* package.json */
{
    "name": "node-echo",
    "main": "./lib/echo.js"
}
```
使用`node_modules`目录直接使用第三方包名加载模块。定义`package.json`后，将`node_echo`当作一个包使用。

# 链接
学习链接：[七天学会NodeJS](https://nqdeng.github.io/7-days-nodejs/#1.5)

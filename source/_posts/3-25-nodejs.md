---
title: "[NodeJS]文件操作"
date: 2024-03-25 21:11:10
tags: [web,后端]
categories: 笔记
toc: true
cover: https://pplware.sapo.pt/wp-content/uploads/2019/10/nodejs_capa.jpg
---
# 有啥API

## Buffer(数据块)

[官方文档](http://nodejs.org/api/buffer.html)

JS中只有字符串数据类型，而没有二进制类型，所以NodeJS提供了一个和`String`对等的全局构造函数`Buffer`来提供对二进制数据的操作。我们可以通过读取文件得到`Buffer`的实例，也可以直接构造。

```
let bin = new Buffer([0x68, 0x65, 0x6c, 0x6f ]);`
```

`Buffer`和字符串类似，可以使用`.length`属性得到字节长度。也可以使用`[index]`方式读取指定位置的字节。

`Buffer`可以与字符串相互转化，

```
let str = bin.toString('utf-8'); // => "hello"
```

```
let bin = new Buffer('hello', 'utf-8'); // => <Buffer 68 65 6c 6c 6f>
```

但是，`Buffer`和`String`类型有一个重要区别。字符串是只读的，并且对字符串进行任何修改得到的都将是一个新的字符串，原本的字符串保持不变。`Buffer`更像是C语言中的数组，可以通过`[index]`的方式直接修改某个位置的字节。

```
bin[0] = 0x48;
```

`.slice`**方法**也不是返回一个副本，而是返回了指向原`Buffer`中，某个位置的指针。

```
[ 0x68, 0x65, 0x6c, 0x6c, 0x6f ]
    ^           ^
    |           |
   bin     bin.slice(2)
```

所以，若想拷贝一份`Buffer`，首先要创建一份新的`Buffer`，然后使用`.copy`**方法**将原来的数据赋值过去：

```
let bin = new Buffer([ 0x68, 0x65, 0x6c, 0x6c, 0x6f ]);
let dup = new Buffer(bin.length);

bin.copy(dup);
dup[0] = 0x48;
console.log(bin); // => <Buffer 68 65 6c 6c 6f>
console.log(dup); // => <Buffer 48 65 65 6c 6f>
```
总之，Buffer将JS的数据处理能力从字符串扩展到了任意二进制数据。

## Stream(数据流)

[官方文档](http://nodejs.org/api/stream.html)

当内存中无一次性装下所有需要处理的数据时，一边读取一边处理更加高效。这里，我们使用数据流，NodeJS中通过各种`Stream`来提供对于数据流的操作。

```
let rs = fs.createReadStream(pathname);

rs.on('data', function (chunk) {
    doSomething(chunk);
});

rs.on('end', function () {
    cleanUp();
});
```
上述代码中`data`事件会源源不断的触发，不管`doSomething`函数是否处理得过来。

```
let rs = fs.createReadStream(src);

rs.on('data', function (chunk) {
    rs.pause();
    doSomething(chunk, function () {
        rs.resume();
    });
});

rs.on('end', function () {
    cleanUp();
});
```
这里为`doSomething`加上了回调函数，因此我们可以在处理数据前暂停数据读取，并在处理数据后继续读取数据。

### tip
`Stream`基于事件机制工作，所有`Stream`的实例都继承于NodeJS提供的EventEmitter。

## File System（文件系统）

[官方文档](http://nodejs.org/api/fs.html)

NodeJS通过`fs`内置模块提供对文件的操作。，其API基本分为三类：
- 文件属性读写
    其中常用的有`fs.stat`、`fs.chomd`、`fs.chowmn`

- 文件内容读写
    其中常用的有`fs.readfile`、`fs.readdir`、 `fs.mkdir`

- 底层文件操作
    其中常用的有`fs.open`、`fs.read`、`fs.close`

NodeJS最精华的异步IO模型在`fs`模块里有着充分的体现，例如上边提到的这些API都通过回调函数传递结果。

几乎所有的`fs`模块API的回调参数都存在两个，第一个在错误发生时等于异常对象，第二个参数始终用于返回API方法的执行结果。

而且，基本所有的`fs`模块都有异步进而同步版本。同步API除了方法名的末尾多了一个`Sync`之外，异常对象与执行结果的传递方式也有相应变化。

fs模块提供的API很多，这里不一一介绍，需要时请自行查阅官方文档。

# 遍历目录 
遍历目录是操作文件时的一个常见需求。比如写一个程序，需要找到并处理指定目录下的所有JS文件时，就需要遍历整个目录。

这里暂时不深究
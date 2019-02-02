# 深入浅出 Node.js 读书笔记

## Chapter 1 Node简介

Node 的特点：
1. 异步 I/O
2. 事件与回调函数机制
3. 单线程
> Node 采用 child_process 的方式来解决单线程中大计算量的问题：将计算量分发到各个子进程，将大量的计算分解，然后再通过进程之间的事件消息来传递结果，从而保持应用模型的简单和低依赖。
4. 跨平台

主要的应用场景：
1. 擅长 I/O 密集型应用场景
2. CPU 密集型应用偏弱，但性能仍不俗，也可以通过编写 C/C++ 扩展的方式加强。
3. 与遗留系统和平共处
4. 分布式应用（阿里的 NodeFox， ITier）

## Chapter 2 模块机制
Node 普遍采用 CommonJS 模块规范。

### CommonJS 模块规范
1. 模块引用：
```js
    var math = require('math')
```

2. 模块定义
使用 `exports` 对象作为**导出当前模块的方法或者变量的唯一出口**。
在模块中，还存在一个 `module` 对象，它代表模块自身，而`exports`是`module`的属性。
```js
    // 模块定义
    // math.js
    exports.add = function () {
        ...
    }
    // 调用方法
    // program.js
    var math = require('math')
    exports.increment = function (val) {
        return math.add(val, 1)
    }
```

**`exports` 和 `module.exports` 的区别**

 - `exports` 是对 `module` 的 `exports` 属性的引用。
 - 在使用 `exports.add = function () ` 和 `module.exports.add = function()` 的方式输出模块时，二者没有区别；
 - 但如果使用 `exports = add ()` 的情况，`exports` 这个引用指向的地址就发生了改变。
 - 但你可以使用 `module.exports = add()` 的方法输出模块，因为 `require()` 读取的就是 `module.exports` 对象。它还可以输出除了对象和函数外的基本类型，如 string, number 等。
 - 建议使用第二点的输出方式。
> 引申阅读：[exports 和 module.exports 的区别](https://cnodejs.org/topic/5231a630101e574521e45ef8)


3. 模块标识
模块标识其实就是传递给 `require()` 方法的参数。它必须是符合小驼峰命名的字符串，或者以 `.` 或 `..` 开头的相对路径，或绝对路径。它可以没有任何文件名后缀如 `.js`。

> 引申：[CommonJS 和 ES6 import 的区别](https://wmaqingbo.github.io/blog/2017/09/15/ES6%E6%A8%A1%E5%9D%97-%E5%92%8C-CommonJS-%E7%9A%84%E5%8C%BA%E5%88%AB/)

### Node 的模块实现
在 Node 中引入模块需要经历三个步骤：(1)模块标识符分析(2)文件定位(3)编译执行。

**模块加载过程**
1. 优先从缓存加载
Node 会优先检查缓存，以减少二次引入时的开销。核心模块的缓存检查优先于文件模块的缓存检查，但缓存检查总是第一优先级的。
2. 模块标识符分析
    (1) 核心模块：优先级仅次于缓存加载，在 Node 源代码编译过程中已经编译为二进制代码，加载过程最快。
    (2) 路径形式的文件模块：分析文件模块时，`require()` 方法会将路径转为真实路径，并以真实路径作为索引，将编译后的结果放到缓存中。加载速度慢于核心模块。
    (3) 自定义模块：
3. 文件定位
    (1) 扩展名分析：CommonJS 规范允许标识符中不包含文件扩展名，在这种情况下 Node 会按照 .js, .json, .node 的顺序补足扩展名，依次尝试。这个过程需要调用fs模块同步阻塞地判断文件是否存在，可能引起性能问题。建议：如果是 .json 或 .node 文件，建议加上扩展名，同时配合缓存。
    (2) 目录分析和包：
4. 模块编译
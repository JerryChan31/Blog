# 浏览器 API 
本文是一些使用浏览器API的经验记录。

使用前建议查看[Can I use](https://caniuse.com/)来确认浏览器兼容性。

## 元素————视口相对位置

### Element.getBoundingClientRect()

Element.getBoundingClientRect()方法返回元素的大小及其相对于视口的位置。

![getBoundingClientRect](https://github.com/JerryChan31/Blog/blob/master/asset/getBoundingClientRect.png)

用法：
``` js
rectObject = object.getBoundingClientRect();
console.log(object.top)
console.log(object.left)
console.log(object.right)
console.log(object.bottom)
```

### IntersectionObserver
IntersectionObserver 是一个比较新的浏览器 API，这是一篇详细的介绍。
[intersectionObserver - ruanyf](http://www.ruanyifeng.com/blog/2016/11/intersectionobserver_api.html)

用法：
```JavaScript
// 构造函数
var io = new IntersectionObserver(callback, option);

// 开始观察
// 可多次调用，观察多个节点
io.observe(document.getElementById('example'));

// 停止观察
io.unobserve(element);

// 关闭观察器
io.disconnect();
```

**callback**：目标元素的可见性变化时，就会调用观察器的回调函数`callback`。`callback`一般会触发两次。一次是目标元素刚刚进入视口（开始可见），另一次是完全离开视口（开始不可见）。

`callback`接受一个数组`entries`作为参数，数组内每个成员都是一个`IntersectionObserverEntry`对象。如果有两个被观察对象的可见性发生变化，`entries`数组就会有两个成员。

`IntersectionObserverEntry`的结构如下：
```JavaScript
{
  time: 3893.92, // 可见性发生变化的时间，是一个高精度时间戳，单位为毫秒
  rootBounds: ClientRect { //根元素的矩形区域的信息，getBoundingClientRect()方法的返回值，如果没有根元素（即直接相对于视口滚动），则返回null
    bottom: 920,
    height: 1024,
    left: 0,
    right: 1024,
    top: 0,
    width: 920
  },
  boundingClientRect: ClientRect { // 目标元素的矩形区域的信息
     // ...
  },
  intersectionRect: ClientRect { // 目标元素与视口（或根元素）的交叉区域的信息
    // ...
  },
  intersectionRatio: 0.54, //目标元素的可见比例，即intersectionRect占boundingClientRect的比例，完全可见时为1，完全不可见时小于等于0
  target: element // 被观察的目标元素，是一个 DOM 节点对象
}
```

**Option**：提供额外的配置项。
1. threshold 属性：`threshold`属性决定了什么时候触发回调函数。它是一个数组，每个成员都是一个门槛值，默认为[0]，即交叉比例（intersectionRatio）达到0时触发回调函数。
2. root 属性：很多时候，目标元素不仅会随着窗口滚动，还会在容器里面滚动（比如在iframe窗口里滚动）。容器内滚动也会影响目标元素的可见性，IntersectionObserver API 支持容器内滚动。`root`属性指定目标元素所在的容器节点（即根元素）。注意，容器元素必须是目标元素的祖先节点。
3. rootMargin属性：定义根元素的margin，用来扩展或缩小`rootBounds`这个矩形的大小，从而影响intersectionRect交叉区域的大小。它使用CSS的定义方法，比如10px 20px 30px 40px，表示 top、right、bottom 和 left 四个方向的值。懒加载的实现里，如果需要提前加载，可以使用这个属性，使得元素在进入视口前就加载好。

IntersectionObserver的实现采用了`requestIdleCallback()`，即只有线程空闲下来，才会执行观察器。这意味着，这个观察器的优先级非常低，只在其他任务执行完，浏览器有了空闲才会执行。

### WHICH TO USE?
如果是想监听鼠标滚动/滚动条拖动等页面滚动过程中元素和视口的交叉状态，请使用`intersectionObserver`，因为它性能更好。
如果是通过鼠标点击等事件触发，判断元素和视口的交叉状态，则可以使用`getBoundingClientRect`，因为它调用更简单，兼容性更好。

选择的关键：如果通过scroll事件来监听`getBoundingClientRect`的变化，容易造成性能问题。
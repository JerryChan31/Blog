# 移动端浏览器触摸手势 - 开发经验分享

## 引言

> 我们已经迈入触摸屏的时代了，而手势作为一个在触摸屏上最直接，最自然的交互方式也越来越受到重视，做好对触摸的优化，对于优化移动端的体验是非常有益的。
之前产品提了一个移动端图片查看器的需求，在完成需求的过程中研究了一下触摸手势的实现，了解了一下移动端手势开发的相关知识，今天就来跟大家作一个简单的分享。😉

## 浏览器API
引用自[MDN - touch events](https://developer.mozilla.org/zh-CN/docs/Web/API/Touch_events)

### TouchEvent
 - 表示位于表面上的一个触摸点的某个状态发生改变时产生的**事件**。包括四种类型：
     - `touchstart`
     当用户在触摸平面上放置了一个触点时触发。事件的目标 element 将是触点位置上的那个目标 element。
     - `touchend`
     当一个触点被用户从触摸平面上移除（当用户将一个手指离开触摸平面）时触发。当触点移出触摸平面的边界时也将触发。例如用户将手指划出屏幕边缘。
     - `touchmove`
     当用户在触摸平面上移动触点时触发。事件的目标 `element` 和这个 `touchmove` 事件对应的 `touchstart` 事件的目标 `element` 相同，哪怕当 `touchmove` 事件触发时，触点已经移出了该 `element` 。
     - `touchcancel`
     当触点由于某些原因被中断时触发。有几种可能的原因如下（具体的原因根据不同的设备和浏览器有所不同）：
         - 由于某个事件取消了触摸：例如触摸过程被一个模态的弹出框打断。
         - 触点离开了文档窗口，而进入了浏览器的界面元素、插件或者其他外部内容区域。
         - 当用户产生的触点个数超过了设备支持的个数，从而导致 TouchList 中最早的 Touch 对象被取消。

 - 每个事件类型的属性包括：
    - `changedTouches`
      一个 `TouchList` 对象，包含了代表所有从上一次触摸事件到此次事件过程中，状态发生了改变的触点的 `Touch` 对象。
    - `targetTouches`
      一个 `TouchList` 对象，是包含了如下触点的 `Touch` 对象：触摸起始于 当前事件的目标 `element`上，并且仍然没有离开触摸平面的触点。
    - `touches`
      一个 `TouchList` 对象，包含了所有当前接触触摸平面的触点的 Touch 对象，无论它们的起始于哪个 element 上，也无论它们状态是否发生了变化。


### Touch
表示用户与触摸表面间的一个单独的接触点。包括如下属性：
   - `Touch.identifier` 此 Touch 对象的唯一标识符，一次触摸动作在平面上移动的整个过程中, 该标识符不变
   - `Touch.screenX`，`Touch.screenY` 触点相对于屏幕的坐标
   - `Touch.clientX`，`Touch.clientY` 触点相对于可见视区的坐标
   - `Touch.pageX`，`Touch.pageY` 触点相对于HTML文档的坐标
   - `Touch.radiusX`，`Touch.radiusY` 能够包围用户和触摸平面的接触面的最小椭圆的X，Y轴半径
   - `Touch.rotationAngle` 它是这样一个角度值：由radiusX 和 radiusY 描述的正方向的椭圆，需要通过顺时针旋转这个角度值，才能最精确地覆盖住用户和触摸平面的接触面。
   - `Touch.force` 力度
   - `Touch.target` 触点开始的元素。
### TouchList
表示一组 Touch，用于多点触控的情况。

> 注：
> 1. 移动端所用到的触摸手势，都是由以上的API组合调用组成。
> 2. 在进行开发时，要关注不同浏览器有不同的默认行为。
> 3. PC端Chrome浏览器可以模拟单点触摸，但是无法模拟多点触摸。
> 4. 建议实机测试，关注性能。

## Hammer.js
👍 Hammer.js 是一个开源手势库，支持触摸，鼠标，指针事件。无依赖，跨平台/跨浏览器支持好，是一个功能非常完整、强大的手势库。

[Hammer.js - Github](http://hammerjs.github.io/)
 - GitHub stars数量接近2万
 - 支持手势包括：
     - 拖动（Pan）
     - 双指缩放（Pinch）
     - 长按（Press）
     - 双指旋转（Rotate）
     - 滑动（Swipe）
     - 短按（Tap）
  - gzipped 大小 7.34kb

接下来我们就来看一下，Hammer.js的大致实现。

### 全局管理 - Manager 类
 - 采用了 Manager 的设计模式，Manager类负责管理全局的状态和流程，输入/手势识别器/触摸动作实例的创建和销毁。（manager.js)
 - Manager 的架构：
 ![](https://github.com/JerryChan31/Blog/blob/master/asset/Hammerjs%E6%9E%B6%E6%9E%84.png)
### 处理输入 - Input 类
 - 将不同形式的输入（如鼠标，触摸等）统一化。（/input)
 - 根据触摸点的信息计算出手势的信息，如 direction, angle, rotation, velocity 等等。(/inputjs)
 - 将信息返回给 Manager 类。(input-handler.js)

### 识别手势 - Recognizer类
 - Recognizer 类的结构：
![](https://github.com/JerryChan31/Blog/blob/master/asset/recognizer.jpg)
 - 当一个输入会话（input session）开始时，所有的识别器都是`Possible`状态。
 - 输入会话的定义：从第一个输入到最后一个输入，其中的所有动作。
 - 每个识别周期都会执行`manager.recognize()`来更新识别器的状态。
 - 如果识别器的状态变成了`FAILED`，`CANCELLED`，`RECOGNIZED`或`ENDED`，它的状态会被重设为`Possible`，在下一个识别周期重新参与识别。
 - 识别结果返回到 Manager 类，通过`manager.emit()`抛出手势事件。
``` 
flow：
                Possible
                   |
             +-----+---------------+
             |                     |
       +-----+-----+               |
       |           |               |
    Failed      Cancelled          |
                           +-------+------+
                           |              |
                       Recognized       Began
                                          |
                                       Changed
                                          |
                                   Ended/Recognized
```
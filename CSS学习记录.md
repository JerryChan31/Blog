# CSS学习记录
此博客用于记录常用但又不太熟悉的 CSS 相关知识，会持续更新。

## 元素水平居中
 - 块级元素
```CSS
    margin: 0 auto 0 auto;
```
 - 行内元素
```CSS
    // 父元素：
    text-align: center;
```

## 元素垂直居中
 - 块级元素
```CSS
方法1：
    // 父元素：
    position: relative;
    // 子元素：
    position: abosulte;
    top: 0
    left: 0
    right: 0
    bottom: 0
    margin: auto
方法2：
    // 父元素：
    position: relative;
    // 子元素：
    position: absolute;
    top: 50%
    left: 50%
    margin-left: -(@width / 2);
    margin-top: -(@height / 2);
方法3：
    // 父元素：
    position: relative;
    // 子元素：
    position: absolute;
    top: 50%
    left: 50%
    transform: transition(-50%, -50%);
方法4：
    // 父元素：
    display: flex;
    justify-content: center;
    align-items: center;
```
 - 行内元素
```CSS
单行文本：
    // 父元素：
    line-height: @height;
多行文本：
    // 父元素：
    line-height: @height;
    // 子元素：
    display: inline-block;
    line-height: 20px; //reset
    vertical-align: middle;
```

## z-index的注意事项
 - **双因素决定堆叠顺序：层叠级别、层叠上下文**：元素在 "Z 轴" 方向上的呈现顺序，由层叠上下文和层叠级别决定。在文档中，每个元素仅属于一个层叠上下文。在给定的层叠上下文中，每个元素都有一个整型的层叠级别，它描述了在相同层叠上下文中元素在 "Z轴" 上的显示顺序；
 - **同一个层叠上下文**中，层叠级别大的显示在上，层叠级别小的显示在下，相同层叠级别时，遵循后来居上的原则（back-to-font）；
 - **不同层叠上下文**中，元素显示顺序以父级层叠上下文的层叠级别来决定显示的先后顺序。与自身的层叠级别无关；
 - 根元素形成 root stacking context，而其他的 stacking context 则由定位元素产生（此定位元素的 z-index 被定义一个非 auto 的 z-index 值），定位子元素会以这个 local stacking context 为参考，用相同的规则来决定层叠顺序；
 - 当任何一个元素层叠另一个包含在不同 stacking context 元素时，则会以 stacking context 的层叠级别（stack level）来决定显示的先后情况。也就是说，**在相同的 stacking context 下才会用元素本身的 z-index 来决定先后，不同时则由 stacking context 的父元素的 z-index 来决定。**

 简单总结：在父元素指定了`z-index`的情况下，父元素覆盖子元素的`z-index`属性。

## 图片样式

**自适应等比缩放：**
```CSS
.image {
    width: auto;
    height: auto;
    max-width: 100%;
    max-height: 100%;
}
```

## pointer-events
>MDN: pointer-events CSS 属性指定在什么情况下 (如果有) 某个特定的图形元素可以成为鼠标事件的 target。

 - 常用值：
auto：与`pointer-events`属性未指定时的表现效果相同，对于SVG内容，该值与`visiblePainted`效果相同
none：元素永远不会成为鼠标事件的`target`。但是，当其后代元素的`pointer-events`属性指定其他值时，鼠标事件可以指向后代元素，在这种情况下，鼠标事件将在捕获或冒泡阶段触发父元素的事件侦听器。
 - 只能在SVG内容上使用的值：（不做详细介绍）
visiblePainted; /* SVG only */
visibleFill;    /* SVG only */
visibleStroke;  /* SVG only */
visible;        /* SVG only */
painted;        /* SVG only */
fill;           /* SVG only */
stroke;         /* SVG only */
all;            /* SVG only */

>该属性也可用来提高滚动时的帧频。的确，当滚动时，鼠标悬停在某些元素上，则触发其上的hover效果，然而这些影响通常不被用户注意，并多半导致滚动出现问题。对body元素应用pointer-events：none，禁用了包括hover在内的鼠标事件，从而提高滚动性能。

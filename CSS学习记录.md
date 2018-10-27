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
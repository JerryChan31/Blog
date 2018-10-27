<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Flex 布局笔记](#flex-%E5%B8%83%E5%B1%80%E7%AC%94%E8%AE%B0)
  - [什么是 Flex 布局](#%E4%BB%80%E4%B9%88%E6%98%AF-flex-%E5%B8%83%E5%B1%80)
  - [Flex 布局的基本概念](#flex-%E5%B8%83%E5%B1%80%E7%9A%84%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5)
  - [Flex 布局使用的属性](#flex-%E5%B8%83%E5%B1%80%E4%BD%BF%E7%94%A8%E7%9A%84%E5%B1%9E%E6%80%A7)
  - [常见布局的 Flex 写法](#%E5%B8%B8%E8%A7%81%E5%B8%83%E5%B1%80%E7%9A%84-flex-%E5%86%99%E6%B3%95)
    - [使用 flex 实现左侧固定右侧浮动两栏布局](#%E4%BD%BF%E7%94%A8-flex-%E5%AE%9E%E7%8E%B0%E5%B7%A6%E4%BE%A7%E5%9B%BA%E5%AE%9A%E5%8F%B3%E4%BE%A7%E6%B5%AE%E5%8A%A8%E4%B8%A4%E6%A0%8F%E5%B8%83%E5%B1%80)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# Flex 布局笔记

## 什么是 Flex 布局

Flexible Box 模型，通常被称为 flexbox，是一种一维的布局模型。它给 flexbox 的子元素之间提供了强大的空间分布和对齐能力。

CSS 标准为我们提供了 3 种布局方式：标准文档流、浮动布局和定位布局。这几种方式的搭配使用可以轻松搞定 PC 端页面的常见需求，但有着**缺少语义、不够灵活**的缺点。我们需要的是通过 1 个属性就能优雅的实现子元素居中或均匀分布，甚至可以随着窗口缩放自动适应。在这样的需求下，CSS 的第 4 种布局方式诞生了，这就是我们今天要重点介绍的 flex 布局。

## Flex 布局的基本概念

**「容器」**：采用 Flex 布局的元素，称为 Flex 容器（flex container），简称「容器」。

**「项目」**：它的所有子元素自动成为容器成员，称为 Flex 项目（flex item），简称「项目」。

容器默认存在两根轴：水平的 **主轴（main axis）** 和垂直的 **交叉轴（cross axis）**。

主轴的开始位置（与边框的交叉点）叫做main start，结束位置叫做main end；交叉轴的开始位置叫做cross start，结束位置叫做cross end。

项目默认沿主轴排列。单个项目占据的主轴空间叫做main size，占据的交叉轴空间叫做cross size。

![](https://github.com/JerryChan31/Blog/blob/master/asset/CSS3-Flexbox-Model.jpg)

## Flex 布局使用的属性

容器一共有 6 个属性：（括号内为可能的值，加粗为默认值）
 - flex-direction（**row** | row-reverse | column | column-reverse）

    主轴方向：默认row为水平方向从左到右。
 - flex-wrap（**nowrap** | wrap | wrap-reverse）

    元素排列是否换行。
 - flex-flow

    flex-direction 和 flex-wrap 属性的简写形式。默认值为 **row nowrap**。
 - justify-content（**flex-start** | flex-end | center | space-between | space-around）

    项目在主轴上的对齐方式。
 - align-items（flex-start | flex-end | center | baseline | **stretch**）

    项目在交叉轴上的对齐方式。 
 - align-content（flex-start | flex-end | center | space-between | space-around | **stretch**）

    多根轴线之间的对齐方式。

项目一共有 6 个属性：
 - order（integer）

    项目排列顺序，越小排列越靠前，默认为 **0**。
 - flex-grow（number）

    项目的放大比例，默认为 **0**，即如果存在剩余空间也不放大。
 - flex-shrink（number）

    项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。
    
    如果所有项目的flex-shrink属性都为1，当空间不足时，都将等比例缩小。
    
    如果一个项目的flex-shrink属性为0，其他项目都为1，则空间不足时，前者不缩小。
    
    负值对该属性无效。
 - flex-basis（length | **auto**）

    flex-basis属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。
 - flex

    flex-grow, flex-shrink 和 flex-basis的简写，默认值为 **0 1 auto**。后两个属性可选。
 - align-self（**auto** | flex-start | flex-end | center | baseline | stretch）

    align-self属性允许单个项目有与其他项目不一样的对齐方式，可覆盖 align-items 属性。
    默认值为 **auto**，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch。

## 常见布局的 Flex 写法

本栏目记录自己实际运用过程中遇到的情况和解决办法。
一些常见布局的写法可以在[阮一峰的博客](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)中找到。

### 使用 flex 实现左侧固定右侧浮动两栏布局
```CSS
    .wrapper-flex {
        display: flex;
        align-items: flex-start;
    }

    .wrapper-flex .left {
        flex: 0 0 auto;
    }

    .wrapper-flex .right {
        flex: 1 1 auto;
    }
```
**需要注意**：flex容器的一个默认属性值:`align-items: stretch;`。这个属性导致了列等高的效果。 为了让两个盒子高度自动，需要设置: `align-items: flex-start;`


本文基于[阮一峰的博客](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)整理而成，在此表示感谢。

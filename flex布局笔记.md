<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Flex 布局笔记](#flex-%E5%B8%83%E5%B1%80%E7%AC%94%E8%AE%B0)
  - [使用 flex 实现左侧固定右侧浮动两栏布局](#%E4%BD%BF%E7%94%A8-flex-%E5%AE%9E%E7%8E%B0%E5%B7%A6%E4%BE%A7%E5%9B%BA%E5%AE%9A%E5%8F%B3%E4%BE%A7%E6%B5%AE%E5%8A%A8%E4%B8%A4%E6%A0%8F%E5%B8%83%E5%B1%80)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# Flex 布局笔记

## 使用 flex 实现左侧固定右侧浮动两栏布局

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

**需要注意**：flex容器的一个默认属性值:`align-items: stretch;`。这个属性导致了列等高的效果。 为了让两个盒子高度自动，需要设置: `align-items: flex-start;`
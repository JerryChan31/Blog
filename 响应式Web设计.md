# Advanced Styling with Responsive Design
此笔记为Coursera同名课程笔记。

## Week1

### 什么是响应式设计？
响应式设计：
 - It is designing your sites with multiple screen sizes/resolutions in mind.
 - Sites should "work" under any platform, any browser size, any orientation.The user should have the power.

 ### 需要考虑的概念 Concepts to consider
 - Media queries - decting the viewport size 检测
 - Flexible grid-based layout for the relative sizeing
 - Flexible images

 [这里](https://mediaqueri.es/)是一个响应式设计网站的范例。

 测试网站是否响应式的方法：
 1. 调整浏览器大小。
 2. 使用[ami.responsivedesign.is](http://ami.responsivedesign.is/)帮助判断。
 3. 使用 Chrome/Firefox Dev Tools。

### 让网页适应页面大小的三种选项
1. Responsive Web Design(RWD) - fluid measurements, flexible grids, and varying CSS rules.同一套代码，通过检测`meta name=viewport`实现。能优化搜索引擎分数。
2. Adaptive Design - returns one of multiple versions of a page based on the type of device.根据不同设备返回不同代码。
3. Seperate Mobile Site - a seperate page URL for the mobile site.

### Why RWD? 响应式页面设计的优点？
 - Easier to share your data with a single URL. 容易分享。
 - Easier to search engine to index the page. 搜索引擎优化。
 - Fewer files = less maintenance. 容易维护。

延伸阅读：**Fluid Measurements 动态单位（em、rem、px...）**
1. [Responsive Web Design: Using Fonts Responsibly by Annarita Tranfici](http://www.sitepoint.com/understanding-responsive-web-design-how-to-manage-fonts/)
2. [Web Design Basics: Rem vs. Em vs. PX - Sizing Elements in CSS by Matthew Davis](https://www.futurehosting.com/blog/web-design-basics-rem-vs-em-vs-px-sizing-elements-in-css/)
3. [What's the Deal with Em and Rem?]( https://codemyviews.com/blog/whats-the-deal-with-em-and-rem)
4. [Font Size Idea: px at the Root, rem for Components, em for Text Elements by Chris Coyier](https://css-tricks.com/rems-ems/ )
5. [综合指南: 何时使用 Em 与 Rem](https://webdesign.tutsplus.com/zh-hans/tutorials/comprehensive-guide-when-to-use-em-vs-rem--cms-23984)

### Fluid Measurements
 - Your content should fit the size contraints of the viewport. 要有普适性，不能只适应特定的大小。
 - Horizontal scrolling is bad. 避免水平滚动。

**Absolute measurements**
 - px
     - 1px 代表显示屏上的一个像素
 - mm, cm, in
     - 用于打印
 - pt
     - 1 point = 1/72 of an inch.
 - pc
     - 1 pica = 12 points.

**Relative measurements**
 - %
     - percentage values that are always relative to another value.
 - em
     - font size of the element.
 - rem
     - font size of the root element.
 - vw
     - 1% of viewport width.
 - vh
     - 1% of viewport height.

> 1em = 12pt = 16px = 100%
1in = 2.54cm = 25.4mm = 72pt = 12pc

## Week 2

### Media Queries

 - Media queries allow the style to depend upon the meida properties.
 - CSS 2.1 used media types.(Just "screen" and "print" option).

**CSS3**

 - CSS 3 increased the capabilities. Style can depend on many features.
     - width, height, orientation, resolution, ...
 - Boolean operators can also be applied to increase power.(and/or)

### The two query components
 1. A media type(screen, print, all, ...)
 2. The actual query of a media feature and "trigger" size(width, height, orientation, resolution, ...)

    example:
    screen and (max-device-width: 480px) and (resolution: 163dpi)

### How to implement media queries

 - Use the @import rule
    @import url(smallstyle.css) screen and (min-width:600px)
 - **Put media query directly in the style sheet(Mostly used)**
    @media screen and (min-width:500px) {...}
 - Include query in the link (not good)
    <link rel = "stylesheet" media="screen and (min-wdith:400px)">

### Wireframes

 - Coding after your design.
 - Wireframe 可以看作布局的草稿，只关注内容和布局
 - Mobile view is the most important view in web design. 移动视图最重要！
 - 总的来说就是要提前想好你的页面要怎么根据浏览器大小变化，先设计再动手！
 - 一些 Wireframe 的规则

### breakpoints

 - Size that define a change in your site layout or content.
 - Used to provide best possible experience for users based on device infomation.

### MOBILE FIRST !!

 - You shouldn't see breakpoints for small screens. The default styling already convers that.
 - You should have min-width instead of max-width
 - 默认设计时，要按照移动视图来进行设计，其次再考虑更大窗口下的展现方式。

 ### Media Queries - Part 2

 **Step 1: Grab infomation**
 The meta viewport tag tells mobile browser's viewport how to behave.
 `<meta name = "viewport" content="width=device-width, initial-scale=1">`
 > BAD practice: 加入maximum-scale=1等限制用户自主选择样式的属性。

 **Step 2: Fluid layout**
 BEST practice: use fluid measurement like `percentage/em/rem`.
 paddings and margins affected by width, not height. 

**Step 3: Media queries**
 Fluid layout that is triggered by certain sizes.
 ORDERING your rules: default rule always on the top.
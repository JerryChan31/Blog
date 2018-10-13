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


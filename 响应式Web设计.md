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

### 响应式的三种选项
1. Responsive Web Design - fluid measurements, flexible grids, and varying CSS rules.同一套代码，通过检测`meta name=viewport`实现。能优化搜索引擎分数。
2. Adaptive Design - returns one of multiple versions of a page based on the type of device.根据不同设备返回不同代码。
3. Seperate Mobile Site - a seperate page URL for the mobile site.
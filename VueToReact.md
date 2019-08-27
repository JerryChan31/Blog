# 从 Vue 到 Taro - 快速上手指南

本文是个人从 Vue 技术栈转移到 Taro 技术栈的一些总结。

## 概览

### 对于不了解 Taro 的朋友

**Taro** 是一套遵循 [React](https://reactjs.org/) 语法规范的 **多端开发** 解决方案。现如今市面上端的形态多种多样，Web、React-Native、微信小程序等各种端大行其道，当业务要求同时在不同的端都要求有所表现的时候，针对不同的端去编写多套代码的成本显然非常高，这时候只编写一套代码就能够适配到多端的能力就显得极为需要。

使用 **Taro**，我们可以只书写一套代码，再通过 **Taro** 的编译工具，将源代码分别编译出可以在不同端（微信/百度/支付宝/字节跳动/QQ小程序、快应用、H5、React-Native 等）运行的代码。

通过编译抹平平台差异，实现 **Write once, run anywhere** 的梦想

### 区别于目前业务中用到的 Vue 技术栈

目前团队在 Taro Hybrid 开发中使用到的、区别与目前业务中用到的 Vue 技术栈的技术主要有：

- React
- Mobx
- Typescript

> [《为何我们要用 React 来写小程序 - Taro 诞生记》这篇文章](https://aotu.io/notes/2018/06/25/the-birth-of-taro/index.html)解释了 Taro 团队为什么选择了 React 语法，摘要下来是以下几点：
>
> 相同点：
>
> - **生命周期**：小程序的生命周期和 React 的生命周期，在很大程度上是类似的。
> - **数据更新方式**：在 React 中，组件的内部数据是用 `state` 来进行管理的，而在小程序中组件的内部数据都是用 `data` 来进行管理，两者具有一定相似性。而同时在 React 中，我们更新数据使用的是 `setState` 方法，传入新的数据或者生成新数据的函数，从而更新相应视图。在小程序中，则对应的有 `setData` 方法，传入新的数据，从而更新视图。
> - **事件绑定**：小程序中绑定事件使用的是 `bind + 事件名` 的方式，React 里，则是 `on + 事件名` 的方式。
>
> 差异：
>
> - **模版**：小程序使用模版字符串，React 使用 JSX。 （ Taro 的做法：使用`babel` 的核心编译器`babylon`构造 AST，对 AST 进行转换操作，得出需要的新 AST，再将新 AST 进行递归遍历，生成小程序的模板。通过穷举的方式，将常用的、React 官方推荐的写法作为转换规则加以支持，而一些比较生僻的，或者是不那么推荐的写的写法则不做支持，转而以 `eslint` 插件的方式，提示用户进行修改。）

## React

### 组件、props 与 state

Vue: 单文件组件，props与data

React: 基于 ES6 class 的组件

```React
class Clock extends React.Component {
  constructor(props) { // 一般在构造函数中初始化 state
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    // 生命周期函数
    // 请求数据……
  }
  
  render() { // 一个组件类必须要实现一个 render 方法，这个 render 方法必须要返回一个JSX 元素
    return (
      <div> 
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```



### 生命周期钩子

**Vue:**

- beforeCreate/created
- beforeMount/mounted
- beforeUpdate/updated
- activated/deactivated
- beforeDestroy/destroyed
- errorCaptured

**React:**

**挂载**：当组件实例被创建并插入 DOM 中时，其生命周期调用顺序如下：

- `constructor()`
- `componentWillMount()`
- `render()`
- `componentDidMount()`

**更新**：当组件的 props 或 state 发生变化时会触发更新。组件更新的生命周期调用顺序如下：

- `componentWillReceiveProps()`
- `shouldComponentUpdate()`
- `componentWillUpdate()`
- `render()`
- `componentDidUpdate()`

**卸载**：当组件从 DOM 中移除时会调用如下方法：

- componentWillUnmount()

**错误处理**：当渲染过程，生命周期，或子组件的构造函数中抛出错误时，会调用如下方法：

- static getDerivedStateFromError()
- componentDidCatch()

![未命名文件](https://github.com/JerryChan31/Blog/raw/master/asset/react-life-cycle.jpg)

> 注：在 React 16.3 中，React团队为`componentWillMount()`，`componentWillUpdate()`，`componentWillReceiveProps()`这三个生命周期钩子加上了 UNSAFE 标记。**React 团队计划在 17.0 中测地废弃掉这几个 API**。改动的原因和异步渲染有关，可能会导致这些生命周期函数重复执行，详见[Update on Async Rendering](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html#initializing-state)。
>
> — 归纳自[谈谈React新的生命周期钩子](https://juejin.im/post/5b72d8fbe51d45662b0752af)

### 组合 VS 继承

> 在 Facebook，我们在成百上千个组件中使用 React。我们并没有发现需要使用继承来构建组件层次的情况。

个人尝试：BaseCard - PicCard - VoteCard

## JSX

### 简介

```jsx
const element = <h1>Hello, world!</h1>;
```

> JSX是一个 JavaScript 的语法扩展。我们建议在 React 中配合使用 JSX，JSX 可以很好地描述 UI 应该呈现出它应有交互的本质形式。JSX 可能会使人联想到模版语言，但它具有 JavaScript 的全部功能。
>
> ——[React - JSX 简介](https://zh-hans.reactjs.org/docs/introducing-jsx.html)

个人理解：写在 Javascript 中的模版语言

### 模版语法

Vue: 

```vue
<span>Message: {{ msg }}</span>
```

JSX:

```jsx
<span>Message: { msg }</span>
```

一些细节：

- JSX 允许在大括号中嵌入任何有效的 Javascript 表达式

- JSX 自动完成了转义，以防止 XSS 攻击

- 每一个 JSX 元素其实都是 `React.createElement()`函数的语法糖

一个简化过的 React 元素实例：

```javascript
// 注意：这是简化过的结构
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world!'
  }
}
```

### 条件渲染

Vue : `v-if`

```vue
<h1 v-if="awesome">Vue is awesome!</h1>
```

JSX : `&&`和 `? :`

```react
{ awesome && <h1> React is awesome! </h1> } // 与运算符
<h1>React is { awesome ? 'very' : 'very very'} awesome!</h1> // 三目运算符
```

**复杂条件** —— 个人认为以**代码可读性**为标准


```jsx
// Example 1
render () {
  const card = this.props.card
  const length:number = card.length
  const renderImgs:JSX.Element[] = []
  if (length > 0 && length < 3) {
    renderImgs.push(<Image className='pic-card__single-pic' src={card.img[0]}/>)
  } else if (length >= 3) {
    for (let i = 0; i < 3; i++) {
      renderImgs.push(<Image className='pic-card__multi-pic' src={card.img[i]}/>)        
    }
  }
  return (
    <BaseCard card={card}>
      {renderImgs}
    </BaseCard>
  )
}

// Example 2
render () {
  const card = this.props.card
  const length:number = card.img.length
  return (
    <BaseCard card={this.props.card}>
      { length > 0 && length < 3 && // 1-2张，显示1张
        <Image className='pic-card__single-pic' src={card.img[0]}></Image>} 
      { length >= 3 && // 大于3图，显示3张
        <View>
          <Image className='pic-card__multi-pic' src={card.img[0]}/>
          <Image className='pic-card__multi-pic' src={card.img[1]}/>
          <Image className='pic-card__multi-pic' src={card.img[2]}/>
        </View>
      }
    </BaseCard>
  )
}
```

### 列表渲染

Vue : `v-for`

```vue
<div v-for="item in items" :key="item.id">
  <!-- 内容 -->
</div>
```

JSX :`map()`

```jsx
<ul>
  { items.map((item) => <ListItem key={item.toString()} value={item} />) }
</ul>
```

注：
1. 与 Vue 相同，每一项都必须设置`key`属性
2. 如果列表项目的顺序可能会变化，则不建议使用索引来用作`key`值，因为这样做会导致性能变差，还可能引起组件状态的问题。如果你选择不指定显式的 key 值，那么 React 将默认使用索引用作为列表项目的 key 值。
3. 如果一个 `map()` 嵌套了太多层级，那可能就是你提取组件的一个好时机。

延伸阅读：[深度解析使用索引作为 key 的负面影响](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318)，[深入解析为什么 key 是必须的](https://zh-hans.reactjs.org/docs/reconciliation.html#recursing-on-children)。

## MobX

### observable

1. 如果 **value** 是ES6的 `Map` : 会返回一个新的 [Observable Map](https://cn.mobx.js.org/refguide/map.html)。如果你不只关注某个特定entry的更改，而且对添加或删除其他entry时也做出反应的话，那么 Observable maps 会非常有用
2. 如果 **value** 是数组，会返回一个 [Observable Array](https://cn.mobx.js.org/refguide/array.html)。
3. 如果 **value** 是没有原型的对象，那么对象会被克隆并且所有的属性都会被转换成可观察的。参见 [Observable Object](https://cn.mobx.js.org/refguide/object.html)。
4. 如果 **value** 是有原型的对象，JavaSript 原始数据类型或者函数，会返回一个 [Boxed Observable](https://cn.mobx.js.org/refguide/boxed.html)。MobX 不会将一个有原型的对象自动转换成可观察的，因为这是它构造函数的职责。在构造函数中使用 `extendObservable` 或者在类定义中使用 `@observable`。
5. 如果 **value** 是有原型的对象，JavaSript 原始数据类型或者函数，`observable` 会抛出。如果想要为这样的值创建一个独立的可观察引用，请使用 [Boxed Observable](https://cn.mobx.js.org/refguide/boxed.html) observable 代替。MobX 不会将一个有原型的对象自动转换成可观察的，因为这是它构造函数的职责。在构造函数中使用 `extendObservable` 或在类定义上使用 `@observable` / `decorate` 。

核心章节：https://cn.mobx.js.org/best/react.html



## Typescript
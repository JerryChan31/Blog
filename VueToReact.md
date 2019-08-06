

# 从 Vue 到 Taro - 快速上手指南

本文是个人从 Vue 技术栈转移到 Taro 技术栈的一些总结。本文试图将 Vue 中的一些概念和 Taro 中的概念对应起来，方便 Vue 的开发者更快地上手 Taro。个人学习 Taro 时间不长，如有错漏，烦请斧正。

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

- Babel 会将 JSX 转译成 `React.createElement()`函数调用

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

！！Taro 用的还是旧的生命周期钩子函数！！**

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

> 注：一些旧的资料/代码/教程中会用到`componentWillMount()`，`componentWillUpdate()`，`componentWillReceiveProps()`这三个生命周期钩子函数。在 React 16.3 中，React团队为上面三个生命周期钩子加上了 UNSAFE 标记。**React 团队计划在 17.0 中测地废弃掉这几个 API**。改动的原因和异步渲染有关，详见[Update on Async Rendering](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html#initializing-state)。
>
> — 归纳自[谈谈React新的生命周期钩子](https://juejin.im/post/5b72d8fbe51d45662b0752af)

## MobX

## Typescript
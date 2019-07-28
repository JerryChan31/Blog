# 从 Vue 到 JSX - 快速上手指南



## 模版语法

Vue: 

```vue
<span>Message: {{ msg }}</span>
```

JSX:

```jsx
<span>Message: { mag }</span>
```

**JSX 允许在大括号中嵌入任何表达式**

## 条件渲染

Vue : `v-if`

```vue
<h1 v-if="awesome">Vue is awesome!</h1>
```

JSX : `&&`和 `? :`

```react
{ awesome && <h1> React is awesome! </h1> } // 与运算符
<h1>React is { awesome ? 'very' : 'very very'} awesome!</h1> // 三目运算符
```

## 列表渲染

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

注：如果一个 `map()` 嵌套了太多层级，那可能就是你提取组件的一个好时机。

## 生命周期钩子

**Vue:**

- beforeCreate/created
- beforeMount/mounted
- beforeUpdate/updated
- activated/deactivated
- beforeDestroy/destroyed
- errorCaptured

大家比较熟悉，不再赘述

**React:**

[生命周期钩子函数图示]([http://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/](http://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/))

**挂载**：当组件实例被创建并插入 DOM 中时，其生命周期调用顺序如下：

- constructor()
- static getDerivedStateFromProps()
- render()
- componentDidMount()

**更新**：当组件的 props 或 state 发生变化时会触发更新。组件更新的生命周期调用顺序如下：

- static getDerivedStateFromProps()
- shouldComponentUpdate()
- render()
- getSnapshotBeforeUpdate()
- componentDidUpdate()

**卸载**：当组件从 DOM 中移除时会调用如下方法：

- componentWillUnmount()

**错误处理**：当渲染过程，生命周期，或子组件的构造函数中抛出错误时，会调用如下方法：

- static getDerivedStateFromError()
- componentDidCatch()

> 注：一些旧的资料/代码/教程中会用到`componentWillMount()`，`componentWillUpdate()`，`componentWillReceiveProps()`这三个生命周期钩子函数。在 React 16.3 中，React团队为上面三个生命周期钩子加上了 UNSAFE 标记。**React 团队计划在 17.0 中测地废弃掉这几个 API**。改动的原因和异步渲染有关，详见[Update on Async Rendering](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html#initializing-state)。
>
> — 引用自[谈谈React新的生命周期钩子](https://juejin.im/post/5b72d8fbe51d45662b0752af)
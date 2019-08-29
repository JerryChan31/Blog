# 从 Vue 到 Taro



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

- `componentWillUnmount()`

![未命名文件](https://github.com/JerryChan31/Blog/blob/master/asset/react-life-cycle.png?raw=true)

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
    renderImgs.push(<Image src={card.img[0]}/>)
  } else if (length >= 3) {
    for (let i = 0; i < 3; i++) {
      renderImgs.push(<Image src={card.img[i]}/>)        
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
        <Image src={card.img[0]}></Image>} 
      { length >= 3 && // 大于3图，显示3张
        <View>
          <Image src={card.img[0]}/>
          <Image src={card.img[1]}/>
          <Image src={card.img[2]}/>
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

Vuex 中的很多概念，都可以在 MobX中找到完全对应的概念：

- `state` --- `observable`
- `getter` --- `computed`
- `mutation` --- `action`
- `action` --- `async action` /` flow`

不同的是，MobX 更灵活，你可以在组件内声明 observable，又或者直接修改 observable 的值。

但在大型项目中的最佳实践和 Vuex 是保持一致的：只使用全局的 store，统一通过 action 来修改 observable 的值。

### 感染性

默认情况下将一个数据结构转换成可观察的是**有感染性的**，这意味着 `observable` 被自动应用于数据结构包含的任何值，或者将来会被该数据结构包含的值。

### 性能优化

> [使用 MobX开发高性能 React 应用](https://foio.github.io/mobx-react/)

React 整个的渲染机制就是在 `state/props` 发生改变的时候，重新渲染所有的节点，构造出新的虚拟 DOM tree 跟原来的 DOM tree 用 Diff 算法进行比较，得到需要更新的地方在批量造作在真实的 DOM 上，由于这样做就减少了对 DOM 的频繁操作，从而提升的性能。

借助于 mobx 框架对 `observable` 变量引用的跟踪和依赖收集，mobx 能够精确地得到 react 组件对 `observable` 变量的依赖图谱，然后再用经典的 `ShallowCompare` 实现细粒度的 `shouldComponentUpdate` 函数，以达到100%无浪费 render 。这一切都是自动完成地，fantastic！使用 mobx 后，我们再也无需手动写 `shouldComponentUpdate` 函数了。

**Transaction**

细粒度带来的另外一个问题：

``` tsx
class TodoItemModel {
    @observable title;
    @observable completed;
    ......
    reset() {
        this.completed = false; // 触发重新渲染
        this.title= ''; // 再次触发重新渲染
    }
    ......
}
```

使用 transaction 解决：

```tsx
class TodoItemModel {
    @observable title;
    @observable completed;
    ......
    reset() {
        transaction(()=>{
            this.completed = false;
            this.title= '';
        }) // 只渲染一次
    }
    ......
}
```



核心章节：[MobX 会对什么作出反应?](https://cn.mobx.js.org/best/react.html)



## Typescript

**TypeScript = Javascript + 静态类型**

Typescript 的优势：

- 类型检查
- 代码提示

缺点：

- 习惯动态类型的程序员，上手难度略高，特别是和`MobX`结合时。

例子：

```tsx
// mobx store
class ExampleStore extends Component {
  @observable val:number = 0
}
export default new ExampleStore()
// component
@inject('ExampleStore')
class ExampleComponent extends Component {
  render () {
		<Text>
    	{this.props.ExampleStore.val} // tsc: this.props 上没有 ExampleStore 这个属性
    </Text>
  }
}

// 添加props声明
interface IProps {
	ExampleStore: any // store的类型是什么？	
}

// 父级组件
class ExampleFatherComponent extends Components {
	render () {
		<ExampleComponent /> // tsc: props 中缺少了 ExampleStore
  }
}

// props修改
interface IProps {
	ExampleStore?: any // 如果给 ExampleStore 加上问号呢？子组件引用属性时会提示可能为 undefined
}
```

最后参考了别人的项目：

```tsx
// mobx store
interface ExampleStoreInterface {
	val: number
}

class ExampleStore implements ExampleStoreInterface {
  @observable val:number = 0
}
export default new ExampleStore()

// component
interface IProps {
	// ...除 store 外的 props
}

interface InjectProps {
	ExampleStore: ExampleStoreInterface
}

@inject('ExampleStore')
class ExampleComponent extends Component<IProps> {
  get inject () {
		return this.props as injectProps
  }
  
  render () {
		<Text>
    	{this.props.ExampleStore.val} // tsc: this.props 上没有 ExampleStore 这个属性
    </Text>
  }
}
```

**在大部分情况下，认真阅读 tsc 的报错就能解决问题。**



# Taro 跨端开发方案

[《为何我们要用 React 来写小程序 - Taro 诞生记》](https://aotu.io/notes/2018/06/25/the-birth-of-taro/index.html)

## 核心：抹平平台差异

输入一份源代码，针对不同的端设定好对应的转换规则，再一键转换出对应端的代码。

![img](http://img11.360buyimg.com/img/jfs/t23863/65/477773801/39493/d1292897/5b307974Na1febb30.jpg)

实际要做的不仅仅是这些，因为不同端会有自己的原生组件，端能力 API 等等，代码直接转换过去后，可能不能直接执行。例如，小程序中普通的容器组件用的是 `<view />`，而在 H5 中则是 `<div />`；小程序中提供了丰富的端能力 API，例如网络请求、文件下载、数据缓存等，而在 H5 中对应功能的 API 则不一致。

为了弥补不同端的差异，我们需要订制好一个统一的组件库标准，以及统一的 API 标准，在不同的端依靠它们的语法与能力去实现这个组件库与 API，同时还要为不同的端编写相应的运行时框架，负责初始化等等操作。

![img](http://img14.360buyimg.com/img/jfs/t21535/241/1645070830/74027/775c8a15/5b307976Nce466138.jpg)



## 限制

> 在 Taro 最初的设计中，我们组件库与 API 的标准就是源自小程序的，因为我们觉得既然已经有定义好的组件库与 API 标准，那为啥不直接拿来使用呢，这样不仅省去了定制标准的冥思苦想，同时也省去了为小程序开发组件库与 API 的麻烦，只需要**让其他端来向小程序靠齐**就好。
>
> 例子：[Image 组件](https://nervjs.github.io/taro/docs/components/media/image.html)

不同端的能力有所差异，在抹平平台差异的过程中，必然会受到**短板效应**的限制。

- 对齐短板（如[样式](https://nervjs.github.io/taro/docs/before-dev-remind.html)）
- 放弃兼容
- 条件编译

假如有一个 `Test` 组件存在微信小程序、百度小程序和 H5 三个不同版本，那么就可以像如下组织代码

`test.js` 文件，这是 `Test` 组件默认的形式，编译到微信小程序、百度小程序和 H5 三端之外的端使用的版本

`test.h5.js` 文件，这是 `Test` 组件的 H5 版本

`test.weapp.js` 文件，这是 `Test` 组件的 微信小程序 版本

`test.swan.js` 文件，这是 `Test` 组件的 百度小程序 版本

`test.qq.js` 文件，这是 `Test` 组件的 QQ 小程序 版本

`test.quickapp.js` 文件，这是 `Test` 组件的 快应用 版本

四个文件，对外暴露的是**统一的接口**，它们接受一致的参数，只是内部有针对各自平台的代码实现

而我们使用 `Test` 组件的时候，引用的方式依然和之前保持一致，`import` 的是不带端类型的文件名，在编译的时候会自动识别并添加端类型后缀。
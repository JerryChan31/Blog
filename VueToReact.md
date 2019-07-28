# 从 Vue 到 JSX - 快速上手指南

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

JSX :

```jsx
<ul>
  { items.map((item) => <ListItem key={item.toString()} value={item} />) }
</ul>
```

注：如果一个 `map()` 嵌套了太多层级，那可能就是你提取组件的一个好时机。

## 生命周期钩子

Vue: beforeCreate - created - beforeMount - mounted - beforeUpdate - updated - 
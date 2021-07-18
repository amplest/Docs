# 前端规范(Vue)

## 共同遵守

### 单文件组件命名

- 命名需多个单词，除根组建 App 外
- 组件种**name**值**必须**定义
- 单词以大写开头
- 组件名应该倾向于完整单词而不是缩写

``` javascript
export default {
 name: 'TodoItem',
 // ...
}
```

### 基础组件命名

- 应用特定样式和约定的基础组件 (也就是展示类的、无逻辑的或无状态的组件) 应该全部以一个特定的前缀开头，比如 Base、App 或 V

``` text
components/
|- BaseButton.vue
|- BaseTable.vue
|- BaseIcon.vue
```

### props

- prop 的定义应该尽量详细，至少需要指定其类型及默认值，特殊情况下需要定义required

``` javascript
props: {
 status: {
 type: String,
 required: true,
 validator: function (value) {
 return [
 'syncing',
 'synced',
 'version-conflict',
 'error'
 ].indexOf(value) !== -1
 }
 }
}
```

### v-for

- v-for 与 **key** 不分离

``` javascript
<ul>
 <li v-for="todo in todos" :key="todo.id">
 {{ todo.text }}
 </li>
</ul>
```

###  v-if 与 v-for

- 不要把 v-if 和 v-for 同时用在同一个元素上。
- 以下两种情况特殊，但是存在最优解决方案，如遇到，按照最优解决方案来进行。
  - 为了过滤一个列表中的项目 (比如 v-for="user in users" v-if="user.isActive")。在这种情形下，请将 users 替换为一个计算属性 (比如 activeUsers)，让其返回过滤后的列表。
  - 为了避免渲染本应该被隐藏的列表 (比如 v-for="user in users" v-if="shouldShowUsers")。这种情形下，请将 v-if 移动至容器元素上 (比如 ul, ol)。

``` javascript
<ul v-if="shouldShowUsers">
 <li v-for="user in users" :key="user.id">
 {{ user.name }}
 </li>
</ul>
```

### 组件样式作用域

- 对于组件库，应该更倾向于选用基于 **class** 的策略而不是 **scoped** 特性。

这让覆写内部样式更容易：使用了常人可理解的 class 名称且没有太高的选择器优先级，而且不太会导致冲突。

``` HTML
<!-- Good -->
<template>
 <button class="c-Button c-Button--close">X</button>
</template>
<!-- 使用 BEM 约定 -->
<style>
.c-Button {
 border: none;
 border-radius: 2px;
}
.c-Button--close {
 background-color: red;
}
</style>
```

### scoped 中的元素选择器

- 元素选择器应该避免在 **scoped** 中出现。
- 在 **scoped** 样式中，类选择器比元素选择器更好，因为大量使用元素选择器是很慢的。


### 模板中的组件名大小写

``` HTML
<!-- 在单文件组件和字符串模板中 -->
<MyComponent/>
```

### 多个特性的元素

- 多个特性的元素应该分多行撰写，每个特性一行。

``` HTML
<img
 src="https://vuejs.org/images/logo.png"
 alt="Vue Logo"
>
<MyComponent
 foo="a"
 bar="b"
 baz="c"
/>
```

### 模板中的表达式

- 组件模板应该只包含简单的表达式，复杂的表达式则应该重构为计算属性或方法。

### 带引号的特性值

非空 HTML 特性值应该始终带引号。

### 指令缩写

都用指令缩写 (用 : 表示 v-bind: 和用 @ 表示 v-on:)

``` HTML
<input
 @input="onInput"
 @focus="onFocus"
>
```

### 多个特性的元素

多个特性的元素应该分多行撰写，每个特性一行。

### 模板中简单的表达式

组件模板应该只包含简单的表达式，复杂的表达式则应该重构为计算属性或方法。


# JavaScript

## 和渲染无关的数据

# 前端规范(Vue)

## HTML

## CSS

## JavaScript

## 组件

1. 组件命名方式

	组件名应该始终是多个单词的，根组件 App 除外

```javascript
export default {
  name: "TodoItem",
  // ...
};
```

2. 组件数据

	组件的 data 必须是一个函数。

	当在组件中使用 data 属性的时候 (除了 new Vue 外的任何地方)，它的值必须是返回一个对象的函数。

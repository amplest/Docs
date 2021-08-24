# 前端开发规范

部分已经在项目`Eslint` + `prettier`中进行体现

## 项目范围

云智屏管理系统项目开发, 默认包含`内容管理系统(CMS)`,`编单系统(mtedit)`, `监播系统(monitor)`

## 图片引用

## 命名规范

### path命名

- 谷歌官方对于使用连字符还是下划线问题的建议是：我们建议您在网址中使用连字符（-）而尽量避免使用下划线 （_）

### 页面命名(view)

- 页面名若为多个单词需遵循`camelCase`, 反之**小写**即可
- 若页面为单页面, 也需存放至文件夹中, 内部`vue`文件以`index`来命名
- 页面中 `name` 值遵循 `PascalCase` **(每个页面必须使用)**

### 组件命名(components)

#### 组件目录

遵循 `PascalCase`

#### 基础组件名

```text
// bad
components/
|- MyButton.vue
|- VueTable.vue
|- Icon.vue

// good
components/
|- BaseButton.vue
|- BaseTable.vue
|- BaseIcon.vue
```

#### 组件名中的单词顺序

```text
components/
|- SearchButtonClear.vue
|- SearchButtonRun.vue
|- SearchInputQuery.vue
|- SearchInputExcludeGlob.vue
|- SettingsCheckboxTerms.vue
|- SettingsCheckboxLaunchOnStartup.vue
```

#### 模板中的组件名大小写

遵循 `PascalCase`

```html
<!-- good -->
<MyComponent />
<!-- bad -->
<mycomponent />
<myComponent />
```

#### Class 命名(style)

遵守 `BEM` 思想, 即, `块`, `元素`, `修饰符` 🔗[BEM Link](https://docs.emmet.io/filters/bem/)

```text
.block
.block__element
.block--modifier
```

#### 变量命名

遵循 `camelCase`

```javascript
let tableTitle = "Table Name";
```

#### 函数命名

遵循 `camelCase` , 前缀为动词

```javascript
//是否可阅读
function canRead(){
    return true;
}
//获取姓名
function getName{
    return this.name
}
```

**Tips: 参考**

| 动词 | 含义                            | 返回值                                                |
| ---- | ------------------------------- | ----------------------------------------------------- |
| can  | 判断是否可执行某个动作 ( 权限 ) | 函数返回一个布尔值。true：可执行；false：不可执行     |
| has  | 判断是否含有某个值              | 函数返回一个布尔值。true：含有此值；false：不含有此值 |
| is   | 判断是否为某个值                | 函数返回一个布尔值。true：为某个值；false：不为某个值 |
| get  | 获取某个值                      | 函数返回一个非布尔值                                  |
| set  | 设置某个值                      | 无返回值、返回是否设置成功或者返回链式对象            |

## 代码规范

### JavaScript

#### 使用严格等

总是使用 `===` 精确的比较操作符, 避免在判断的过程中, 由 `JavaScript` 的强制类型转换所造成的困扰

#### 声明规范

尽量使用 `let` 声明变量, 少用 `var`

#### 策略模式

策略模式的使用，避免过多的`if-else`判断，也可以替代简单逻辑的`switch`

```javascript
// good
const formatDemandItemType2 = (value) => {
  const obj = {
    1: "基础",
    2: "高级",
    3: "VIP",
  };

  return obj[value];
};

// bad
const formatDemandItemType = (value) => {
  switch (value) {
    case 1:
      return "基础";
    case 2:
      return "高级";
    case 3:
      return "VIP";
  }
};
```

#### data 数据层级

一般层级保持 **`2-3`** 层最好

data 数据具有数据层级结构，切勿过度扁平化或者嵌套层级过深，若是过度扁平化会导致数据命名空间冲突，参数传递和处理，若是层级嵌套过深也会导致 vue 数据劫持的时候递归层级过深，若是嵌套层级丧心病狂那种的，小心递归爆栈的问题。

适当的层级结构不仅增加代码的维护和阅读性，还可以增加操作和处理的便捷性

```javascript
// good
{
    person: {
        name: '',
        age: '',
        gender: ''
    }
}

// bad
{
  name: '',
  age: '',
  gender: ''
}
```

### HTML

#### 实体使用

**_`html`_**中展示一些如`<`,`>`,`&`等字符时，使用字符实体代替

```html
<!-- good -->
<div>&gt; 1 &amp; &lt; 12</div>

<!-- bad -->
<div>> 1 & < 12</div>
```

#### 项目 html 内容

`app-main`与`yzp-container`样式不允许做修改, 内容主体均在`yzp-container`, 定义新块级元素独立编写样式等

```html
<template>
  <div class="yzp-container">
    <div class="xxxxx-main">
      // 此处为主体内容
    </div>
  </div>
</template>
```

### CSS

`style` 中 `class` 命名应遵守 `BEM` 思想 🔗[BEM Link](https://docs.emmet.io/filters/bem/)

**Tips:** 不建议在元素上内嵌 style

#### 嵌套层级

浏览器在解析 `css` 时，是按照从右到左递归匹配的，过深的层级嵌套不仅影响性能，而且还会导致样式阅读性和代码维护性降低，一般层架控制在 **`5`** 层之内

#### 样式穿透

使用第三方组件库, 遇到 `scoped` 属性的样式隔离，可能需要去除 `scoped` 或是另起一个 `style` , **但都不推荐**

如果存在预处理器, 则推荐使用如下三种方式来进行样式的修改

- less 使用 `/deep/`
- scss 使用 `::v-deep` (云智屏项目中使用)
- stylus 使用 `>>>`

#### 自有组件样式作用域

对于组件库，应该更倾向于选用基于 `class` 的策略而不是 `scoped` 特性

这让覆写内部样式更容易：使用了常人可理解的 `class` 名称且没有太高的选择器优先级，而且不太会导致冲突

```html
<!-- class 定义 采用BEM 思想 -->
<template>
  <button class="c-Button c-Button--close">X</button>
</template>

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

#### 0 后面不带单位

```css
/* good */
padding-bottom: 0;
margin: 0;
/* bad */
padding-bottom: 0px;
margin: 0em;
```

#### 双引号

属性选择器中的值必须用双引号包围，不允许使用单引号，也不允许不使用引号

#### 属性顺序

同一 规则下的属性在书写时，应按功能进行分组。

并以 `Formatting Model（布局方式、位置）` > `Box Model（尺寸）` > `Typographic（文本相关）` > ` Visual（视觉效果）` 的顺序书写, 提高代码可读性

**Tips:**

- Formatting Model 相关属性包括：position / top / right / bottom / left / float / display / overflow 等
- Box Model 相关属性包括：border / margin / padding / width / height 等
- Typographic 相关属性包括：font / line-height / text-align / word-wrap 等
- Visual 相关属性包括：background / color / transition / list-style 等

如果包含 `content` 属性，应放在属性的最前面

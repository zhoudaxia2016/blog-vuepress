---
title: "css-module"
date: "2020-02-10 11:48:23 +0800"
template: "post"
draft: false
slug: "css-module"
category: "frontend"
tags:
  - "css"
description: "css应该是前端技术中发展比较缓慢的了，本身也具有很多问题。比如没有变量，不能嵌套书写，
代码难以复用等等。不过已经有许多css预处理器可以解决这些问题。但是今天不是讨论这些问题,我们来说说css模块化的问题。
"
---
  
# css模块化

> css应该是前端技术中发展比较缓慢的了，本身也具有很多问题。比如没有变量，不能嵌套书写，
代码难以复用等等。不过已经有许多css预处理器可以解决这些问题。但是今天不是讨论这些问题,我们来说说css模块化的问题。

## 什么是css模块化

首先介绍一下什么是模块化吧。模块化是指将一个复杂问题分为几个互相关联又互不影响的模块，这样比较方便开发和维护。

`css`模块化可以说是将页面的样式分成几部分，它们之间互不影响，不会相互覆盖。但是`css`的作用域是全局的，
也就是说引入一个`css`文件，会对页面所有地方都会影响到。

所以总结来说，`css`模块化要解决两个问题：

1. 模块内的样式不被页面其他地方样式影响。
2. 模块内的样式不要应用到页面其他地方。

## css模块化演化

下面是一些`css`模块化的方案：

### Shadow DOM

`Shadow DOM`是`web`组件化规范的一部分。简单来说就是让一个类似影子的元素插入到页面的某个元素中，且这个影子元素不在整棵`dom`树中，
也就是说这个影子元素有独立的`css`作用域，样式不会被全局样式影响，它的样式也不会应用到`dom`树中。可以说是完美解决了
`css`模块化的问题，不过`Shadow DOM`还没被所有浏览器实现，还不能在生产环境使用。

代码例子；

```javascript
<div><h3>Light DOM</h3></div>
<p><h3>Light DOM</h3></p>
<script>
  var root = document.querySelector('div').createShadowRoot();
root.innerHTML = '<style>h3{ color: red; }</style>' +
               '<h3>Shadow DOM</h3>';
</script>
<style>
h3 {
  color: white;
}
</style>
```

`div`里`h3`和`p`里的`h3`分别应用了不同的样式。

### BEM css类名规范

从另外的角度的话，我们也可以通过对类的命名来实现`css`的模块化。`BEM`规范就是这样一种方案。

`BEM`是指每个类的命名需要遵循**block__element--modifier**的命名规则。只要项目所有地方的代码都
遵循这个规范，就可以让样式都限制在组件之内了。
不过这只是个规范,会出现一些不遵循这个规则的代码，所以不能保证其他样式不会覆盖了使用了`BEM`规范的组件。

`BEM`的另一个缺点就是类命名冗长，书写起来比较麻烦，有时候也会出现不知道怎么起名的难题。

### CSS in JS

相比`css`，`js`则发展得比较快，并且很多任务都可以让`js`去完成。所以衍生出了**CSS in JS**的方案，简单来说就是
在`js`中写`css`。

比如比较出名的**styled-components**，例子如下：

```javascript
import styled from 'styled-components';
const BlueText = styled.p`
  color: blue;
`;
// jsx
<BlueText>My blue text</BlueText>
```

通过`js`的**模板标签**来生成一个带有样式的组件，这样就实现了在`js`中写`css`，并且样式是和组件绑定在一起的，也可以说
实现了`css`模块化。

### css-modules

**css-modules**通过生成类名进行替换实现`css`模块化。

**css-modules**需要打包工具的支持，我们平常使用最多的是`webpack`,在`webpack`的`css-loader`配置中开启**css-modules**：
``` javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        loader: 'css-loader',
        options: {
          modules: true
        }
      }
    ]
  }
}
```
比如下面是一个组件的样式文件:
```
/* style.css */
.button {
  color: red;
}
```

然后就可以直接引用这个样式文件，给组件绑定类名:
```
import style from './style.css'
element.innerHTML = `<div class=${style.button}></div>`
```

最后编译出来的类名可能是_abcdef(可配置)。
这样的话每个组件的类名都是独一无二的，不需要我们在命名上做手脚了，打包工具会帮我们处理。

## vue组件中的css模块化

我们平时使用`vue`开发，`vue`也有自己的`css`模块化方案.

### style标签的scoped属性

我们可以设置vue单文件组件的`style`标签的**scoped**属性，这样的话打包之后`dom`会加上`data-v-xxx`的属性，
而样式的选择器代码对应也会加上`[data-v-xxx]`

下面是一个简单例子：
```
<!-- 源码 -->
<style scoped>
.example {
  color: red;
}
</style>

<template>
  <div class="example">hi</div>
</template>

<!-- 打包代码 -->
<style>
.example[data-v-f3f3eg9] {
  color: red;
}
</style>

<template>
  <div class="example" data-v-f3f3eg9>hi</div>
</template>
```

这样子的话，类`example`的样式就不会影响到其他地方的样式了。

#### 缺点
- 其他组件如果也设置了`example`类名也会影响到上面的组件的。所以还得结合一下`BEM`规范来弥补一下。
- 使用了`scoped`组件每个元素都会加上`data-v-xxx`，比较多余，可能会影响性能。
- 因为加了`data-v-xxx`属性，想覆盖组件样式比较麻烦，需要设置`id`或者`!important`来解决。

### css-modules

`vue-cli3`中默认开启了`css-modules`。在`style`标签设置`module`属性即可使用。

一个简单例子

```
<template>
  <button :class="$style.button" />
</template>

<style module>
  .button {
    color: red
  }
</style>
```

这个方案上面说的两个问题应该都可以解决了，也不需要引入`BEM`规范。不过写起来可能比较麻烦。另一个问题
可能无法覆盖子组件样式。

## 总结

以上列出的一些方案只是`css`模块化的一部分解决方案，还有许多其他方案。没有哪个是最佳方案，
各个项目可能会适合不同的方案，需要根据需求来选择。了解更多的方法，才能选取到最佳的。

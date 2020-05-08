---
title: 组件化思考与elementui学习
date: 2019-11-18
categories: frontend
tags:
  - 组件化
---
  
### 本文目的

最近部门在做一些技术推动，优化等。组件库设计是其中一项。方向上是以elementui为目标，
去借鉴他们的设计。另外一方面最近的项目也涉及到许多需要提取的组件，一边使用elementui
提供的组件进行适配，另一边再思考自己如何去造这个轮子。

### 2019-5-5

今天了解了el-checkbox,el-checkbox-group组件。之前如果我设计checkbox组件的话，是不会设计
两个组件的，也不明白为什么需要两个组件。今天使用之后，有了些许的领悟。

首先呢，我们想让checkbox的样式，逻辑更加可配置一些的话，使用slot是最合适不过的了（之前遇到
类似的问题，都是笨笨的传一个props进去，这样只能配置一些文字而已）。而组件和组件的slot元素之间
通信的问题比较棘手，所以就需要使用一个组件作为一个slot，这里就是ek-checkbox这个组件。
然后通过elementui的emitter mixins，向子孙组件broadcast，或者向祖先组件dispatch。这样我们就能
愉快通信了。比如：
```
<el-checkbox-group v-model="checkList">
  <el-checkbox label="复选框 A"></el-checkbox>
  <el-checkbox label="复选框 B"></el-checkbox>
  <el-checkbox label="复选框 C"></el-checkbox>
</el-checkbox-group>
```
el-checkbox如果被点中了，我们得通知el-checkbox-group来更新checkList的值，这就得在el-checkbox
组件dispatch事件传递到el-checkbox-group，通过它更新checkList的值。如果在slot里不使用组件，
可能通信会比较麻烦。

### 2019-5-6

今天用到了elementui的el-table组件。之前如果不深入思考的话，也没有发现table涉及到这么
多场景需求，不过好在el-table已经将许多需求都考虑到了。比如排序，过滤，汇总，自定义表
头，表项，提示等等。其实这也是组件库设计需要考虑的问题，需要将功能做得多全呢？是否是
越全面越好呢？现在的话，我觉得还是够用就好了，需要对项目里的表格组件做一些调研，看看
需要哪些功能。

虽然el-table功能挺全面的，但是还是遇到了一些问题。

1. 为什么el-table里的元素设置了这么多overflow: hidden?导致我在表头使用了自己写的tooltip
无法显示。最后将所有overflow的样式覆盖才行（elementui的tooltip组件是append到body元素下面的，
所有应该不会有这个问题，为什么tooltip这样设计是另外一个问题了）。

2. 汇总行不能在表头之下的问题。现在只能使用一个普通行作为汇总行，但是这样汇总行也参与排序了。

### 2019-5-7

今天出现一个问题，el-checkbox组件通过for渲染出来时，当删除其中一些checkbox，外层包裹的el-checkbox-group
的选中的数据并不会更新。所以只能在修改el-checkbox数组时，手动处理一下。

在用vue时，当有数据的联动时，发现有时可以使用watch，有时则只能直接在事件里处理。一般情况下，
我比较喜欢用watch或者computed来简化逻辑。但是有些情况下却不是非常适合。比如有两个数组，一个区
数组，一个是这些区所有学校组成的数组。当区数组更新时，学校数组同步更新。

如果使用watch或者computed的化，我只想到通过watch整个区数组来更新学校数组。可是这样一来，每次区数组更新，

都需要遍历区数组来生成学校数组，因为使用watch的话，我们只能获取整个更新后的数据，无法获取添加了或者
删除了哪个区。所以只能在触发更新区数据的代码里，也顺便将学校更新了。

### 2019-5-8

今天在想，有必要将组件库的组件针对业务再封装一遍吗？因为之前总要封装一遍，封装好传props就能正常使用了，
连组件库的使用方式也隐藏起来。另一方面，方便业务复用。

不过很多时候，ui设计的同类的组件并不一样，没什么好复用的，那就没办法了。

这个问题暂时还需要继续思考下去。

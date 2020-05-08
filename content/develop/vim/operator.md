---
title: vim-operator
date: 2019-11-18
categories: develop
tags:
  - vim
---
  
## vim operator

vim的operator是vim非常强大的一个特性，使得vim的编辑操作非常灵活。
每个operator就是vim的一个编辑操作，通过配合count和motion，可以实现
非常多的操作。

下面先详细介绍vim的编辑操作核心。

### count operator motion

vim的每个编辑操作（或者说命令）可以拆分为三个部分：
1. count 次数（可以作用于operator和motion）
2. operator 动作
3. motion 产生动作宾语的移动（除了motion，还可以使用text-object来替代）

比如y是复制的命令，y就是一个operator，y2j表示复制当前行和底下两行，共
三行。j是一个motion，2是这个motion的count，2j表示向下移动两行。则2j产生
的区域就是当前行到移动到底下两行之间的区域。但是这个之间区域该怎么算呢？
算起始位置吗？算结束位置吗？整行都算吗？这些问题可以查看文档关于`linewise`,
`characterwise`,`exclusive`,`inclusive`的说明。

除了motion可以生成一个选中的区域，还可以通过`text-object`。比如iw就是选中
一个单词，不管你在这个单词的哪个字符之下。比如yiw就是复制光标下的单词。

### 自定义选中区域

有时候vim定义的motion和text-object已经不能满足要求，或者说过于麻烦，可以
通过omap来定义自己想要的motion或者text-object。map就是定义快捷键，omap就是
定义当输入operator状态下才生效的快捷键。详细文档可以看`omap-info`

简单例子：
```
omap w iw
```
之后我们在normal模式下按yw就相当于yiw了。但是当单独按w是并不会相当于按了iw

### 自定义operator

vim给我们留下了一个可以自定义的operator——g@。使用g@加上motion或者text-object
之后，时会执行operatorfunc函数，并传入这次选择区域的属性type参数：
1. line 行模式，整行都会被选中
2. char 字符模式
3. block 块模式，选中一个矩形区域

其实这三种情况和三种visual模式一一对应的。可以尝试`v,V,<c-v>`来进入visual模式来
体验一下它们的区别。

这些模式是motion或者text-object的属性，文档中关于某个motion或者text-object的说
明都会展示一下它们是哪个模式的。

除了type参数，我们还可以通过\`[,\`]两个标记来选择被选中的开始位置和结束位置。或者
使用'[,']。\`开头的标记在char模式中使用，'开头的标记一般在line和block模式中使用。

有了这些，我们就能设计出自己的operator（help g@文档有自定义operator的详细例子）。

### 切换line，char和block模式
在使用operator和motion之间插入`V,v`或者`<c-v>`可以切换line，char或block模式。
比如在一行中间，执行y$会复制当前光标下到行尾，这是char模式。但是执行yV$
会将$变成line模式，所以就会复制整行。

### 总结

说了这么多，自定义operator真的有必要吗？下面是一些需求的例子：
1. 快速注释
2. 执行文件里的命令
3. 变量命名模式切换，比如驼峰改成下划线

第二个需求就是触发我去了解operator的原因。q:可以调出我们在命令模式执行的命令历史。
按enter我们可以执行当前行的命令。当时当我需要执行多好命令呢？vim中好像并没有这个
命令，所以我就去寻找这个方法。最终发现这个不是可以用自定义operator来实现吗？这是
多么典型的自定义operator的用途啊！

另一方面，有时候我们并不非常强烈需要我操作的区域，或者这个区域很容易获取，其实我们
并不需要自定义一个operator，map已经可以满足需求。

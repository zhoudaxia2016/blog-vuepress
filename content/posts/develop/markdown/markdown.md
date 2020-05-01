---
title: "一些markdown语法"
date: "2019-11-18 18:01:33 +0800"
template: "post"
draft: false
slug: "markdown-syntax"
category: "develop"
tags:
  - "markdown"
description: "介绍一些常用markdown语法"
---

> gitbbok 配置了一些markdown插件，展示一些效果


## 数学公式（其实也不算markdown语法，引入mathjax插件）

$$1 + 1 = 2$$

质能方程： {% math %}E = mc^2{% endmath %}

欧拉公式：$$e^{i\pi} + 1 = 0$$

莱布尼兹公式：$$\int_a^b f(x) {\rm d}x = F(b) - F(a)$$

## 文字效果
| formatting | code | result |
| :------: | :------: | :------: |
| bold | `**bold**` | **bold** |
| italic | `_italic_` | _italic_ |
| strikethrough | `~strikethrough~` | ~~strikethrough~~ |
| bold and italic | `***bold and italic***` | ***bold and italic*** |

## Todo list
```
- [ ] 找个女朋友
- [ ] 成为有钱人
- [ ] 改变世界
- [x] 独立
```

- [ ] 找个女朋友
- [ ] 成为有钱人
- [ ] 改变世界
- [x] 独立

## 表情
[表情列表](https://www.webfx.com/tools/emoji-cheat-sheet/)

example

| code | result |
| :------: | :------: |
| `:smile:` | :smile: |
| `:yum:` | :yum: |
| `:sunny:` | :sunny: |
| `:girl:` | :girl: |

## 代码高亮

```css
pre[class*="language-"] {
    border: none;
    background-color: #f7f7f7;
    font-size: 1em;
    line-height: 1.2em;
}
```

```js
/**
 * Convert an input value to a number for persistence.
 * If the conversion fails, return original string.
 */
export function toNumber (val: string): number | string {
  const n = parseFloat(val)
  return isNaN(n) ? val : n
}
```


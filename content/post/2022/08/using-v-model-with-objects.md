---
title: Vue.js：自定义组件中对 objects 使用 v-model（译文）
date: 2022-08-29T20:43:15+08:00
draft: true
comments: false
tags: 
- Vue.js
---

> 作者：Simon Kollross
> 
> 原文网址：https://simonkollross.de/posts/vuejs-using-v-model-with-objects-for-custom-components

组件是 Vue.js 最强大的特性。它们允许您将应用程序构建为可重复使用的小型单元，您可以再次使用这些单元来构建新功能。

在构建 SimpleSell 时，我需要捆绑一堆输入字段的自定义组件。它们的行为应该像可重用的输入控件，而它们的状态由对象提供。

# v-model 基础
你当然知道 v-model，这个属性让你的大部分输入元素都能正常工作。但是你知道也可以使用 v-model 作为自己组件的接口吗？

为了更好地理解，让我们看一些基础知识。

```
<input type="text" v-model="value">
```

v-model 无非就是下面的语法糖。
```
<input type="text" :value="value" @input="e => value = e.target.value">
```
首先我们有 `:value` 绑定。它提供输入字段的值。其次是 `@input` 事件。一旦触发了输入事件，我们就用输入元素的当前值更新模型的数据属性值。您也可以使用缩写：`@input="value = $event.target.value"`。

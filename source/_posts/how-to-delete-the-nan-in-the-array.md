---
title: 如何删除数组中的 NaN 值
tags:
  - JavaScript
  - WEB前端
url: 114.html
id: 114
categories:
  - JavaScript
  - WEB 前端
date: 2017-06-28 10:34:28
---

今天在刷 FCC [0](http://(http://www.freecodecamp.cn) "| FreeCodeCamp中文社区") 的时候遇到这么一题 [1](http://(http://www.freecodecamp.cn/challenges/falsy-bouncer) "Falsy Bouncer | FreeCodeCamp中文社区") ，记录一下。

<!-- more -->

##  Falsy Bouncer（真假美猴王）

过滤数组假值 删除数组中的所有假值。 在 `JavaScript` 中，假值有 `false`、`null`、`0`、`""`、`undefined` 和 `NaN`。

```javascript
function bouncer(arr) {
    // 请把你的代码写在这里
    return arr;
}

bouncer([7, "ate", "", false, 9, 0]);
```

##  解

`false`、`null`、`0`、`""`、`undefined` 这几个都很好处理，只要判断是不是等于这些值就好了。就像这样：

```javascript
function bouncer(arr) {
    // 请把你的代码写在这里
    var newArr = arr.filter(function(value) {
        return value != false;
    }).filter(function(value) {
        return value != "0";
    }).filter(function(value) {
        return value != undefined;
    }).filter(function(value) {
        return value != null;
    })
    return newArr;
}

bouncer([7, "ate", "", false, 9, 0]);
```

##  特别的

但是 `NaN` 则不能这么判断，因为 `NaN` 有个不同的地方，就是 `NaN` 不和任何值相等，包括他自己 [3](http://(http://www.shaoqun.com/a/249082.aspx) "[Java教程]js删除数组中的NaN")，也就是说：

```javascript
// in
NaN == NaN

// out
false
```

所以如果使用和之前几个值一样使用 `filter()` [4](http://(https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) "Array.prototype.filter() - JavaScript | MDN") 的话：

```javascript
filter(function(value) {
    return value != NaN; // 这里永远会有返回。
})
```

所以，结合上面说的 `NaN` 的特点，这部分应该这么写：

```javascript
filter(function(value) {
    if (value == value)
        return value; // 对于非 NaN 值来说，永远会有返回值。
})
```

关于题目，到这里就先结束了，发现自己了解的还是太少，等下去总结一下这些“假”值的特点。

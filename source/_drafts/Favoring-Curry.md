---
title: 爱上柯里化
date: 2021-05-21 15:13:52
description: 咖喱？喜欢辣的食物吗？什么？在哪里？
tags:
  - 柯里化
  - 翻译
categories:
  - 翻译
---

我最近写的关于 [Ramda](https://github.com/ramda/ramda) 中函数组合的[文章](http://fr.umio.us/why-ramda/)忽略了一个重要的话题。为了对 Ramda 函数进行我们想要的组合，我们需要对这些函数进行柯里化。

咖喱？喜欢辣的食物吗？什么？在哪里？

实际上，`curry` 是以 Haskell curry 的名字命名的，他是最早研究这种技术的人之一。（是的，他们把他的名字也用在了[函数式编程语言](https://en.wikipedia.org/wiki/Haskell_(programming_language)≈)上；不仅如此，柯里的中间名首字母是 ‘B’，也就是 [Brainf*ck](https://en.wikipedia.org/wiki/Brainfuck≈) 的缩写。）

柯里化是将一个期望有多个参数的函数变成一个在提供较少参数时，返回一个等待剩余参数的新函数的过程。

原始的函数看起来像是这样：

```JavaScript
// 未柯里化版本
var formatName1 = function(first, middle, last) {
    return first + ' ' + middle + ' ' + last;
};

formatName1('John', 'Paul', 'Jones');
//=> 'John Paul Jones' // （呃，是位音乐家还是海军上校？）

formatName1('John', 'Paul');
//=> 'John Paul undefined');
```

但是柯里化过后的版本表现得更有用处：

```JavaScript
// 柯里化版本
var formatNames2 = R.curry(function(first, middle, last) {
    return first + ' ' + middle + ' ' + last;
});

formatNames2('John', 'Paul', 'Jones');
//=> 'John Paul Jones' // （绝对是位音乐家！）

var jp = formatNames2('John', 'Paul'); //=> 返回一个函数
jp('Jones');          //=> 'John Paul Jones'          （也许这个人是海军上将）
jp('Stevens');        //=> 'John Paul Stevens'        （最高法院法官）
jp('Pontiff');        //=> 'John Paul Pontiff'        （好吧，我作弊了）
jp('Ziller');         //=> 'John Paul Ziller'         （魔术师，有点虚构的感觉）
jp('Georgeandringo'); //=> 'John Paul Georgeandringo' （摇滚乐手）
```

或者这样：

```JavaScript
['Jones', 'Stevens', 'Ziller'].map(jp);
//=> ['John Paul Jones', 'John Paul Stevens', 'John Paul Ziller']
```

而且你也可以分多次进行：

```JavaScript
var james = formatNames2('James'); //=> 返回一个函数
james('Byron', 'Dean');            //=> 'James Byron Dean'  （阿飞）
var je = james('Earl');            //=> 当然还是返回一个函数
je('Carter');                      //=> 'James Earl Carter' （总统）
je('Jones');                       //=> 'James Earl Jones'  （演员，黑武士）
```

（有些人坚持认为，我们正在做的事情更应该被称为“局部应用”，而“柯里化”应该每次只接受一个参数，每次函数处理完单个参数后返回一个新的接受单个参数的函数，直到所有必需的函数都已经传入，他们可以坚持他们的观点。）

## 太无聊了吧！你能为我做些什么？

这里有一个稍微有意义的例子。如果你想找到一个数字集合的总和，你可以这样做：

```JavaScript
// Plain JS:
var add = function(a, b) {return a + b;};
var numbers = [1, 2, 3, 4, 5];
var sum = numbers.reduce(add, 0); //=> 15
```

如果你想写一个通用函数，可以将求得任何数字集合的总和，你可以这样写：

```JavaScript
var total = function(list) {
    return list.reduce(add, 0);
};
var sum = total(numbers); //=> 15
```

在 Ramda 中，`total` 和 `sum` 是十分相似的，你可以像这样定义 `sum`：

```JavaScript
var sum = R.reduce(add, 0, numbers); //=> 15
```

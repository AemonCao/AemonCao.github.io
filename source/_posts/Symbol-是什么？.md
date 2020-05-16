---
title: Symbol 是什么？
tags:
  - JavaScript
  - ECMAScript 6
categories:
  - JavaScript
date: 2020-04-25 20:21:04
---


前些天偶然发现了 Symbol，学习了一番，这里记录一下。

<!-- more -->

## Symbol 是什么

symbol 是一种基本数据类型（primitive data type），它是在 ECMAScript 6 引入的一种新的基本数据类型，将 JavaScript 的基本数据类型扩展到了 7 种（分别是 string，number，bigint，boolean，null，undefined 和 symbol）。

## Symbol 有什么用

我们来看一个例子，现在要开发一个学校管理系统简单的权限功能，一共有三种权限：学生、老师和管理员：

```JavaScript
var permissions = {
    student: 'student',
    teacher: 'teacher',
    administrator: 'administrator'
}
```

这样，当我们的功能对于不同权限的人有不同的实现的时候就可以通过以下方法来完成：

```JavaScript
if (user.permission === permissions.student) {
    // do something
} else if (user.permission === permissions.teacher) {
    // do something
} else if (user.permission === permissions.administrator) {
    // do something
}
```

当然也可以用 `switch` 来实现：

```JavaScript
switch (user.permission) {
    case permissions.student:
        // do something
        break
    case permissions.teacher:
        // do something
        break
    case permissions.administrator:
        // do something
        break
    default:
        // do something
        break
}
```

如此，就能根据不同的权限实现不能的功能。

这时，我们回过头来看 `permissions` 的时候会发现：`permissions.student`、`permissions.teacher` 和 `permissions.administrator` 具体取了什么值并不重要，换句话说只要三者的值各不相同即使我们写成下面这样也是无所谓的：

```JavaScript
var permissions = {
    student: 'mv@RR&gr76!6YA',
    teacher: 'GF53@A$o4MWgE$',
    administrator: '5ypSmN4prP#xAb'
}
```

显然，程序也能正常运行。

而 Symbol 就能做到这一点：Symbol 的值都是独一无二的。

可以通过 `symbol()` 来生成一个 symbol 类型的数据：

```JavaScript
var val = Symbol()
```

那我们的代码就可以改写成：

```JavaScript
var permissions = {
    student: Symbol(),
    teacher: Symbol(),
    administrator: Symbol()
}
```

这样就不用再定义一些无意义的字符串来作为判断的值了，代码中就会少很多魔术字符串。

## Symbol 还有什么用

Symbol 还可以做为属性的键名：

```JavaScript
var numKey = Symbol('Number')
var funKey = Symbol('Funtion')

var obj = {
    [numKey]: 1
}

obj[funKey] = function () {
    // do something
}
```

这样的话，我们就不用担心某一个键被不小心覆写或改写。

当我们需要调用的时候，只能通过 Symbol 实例来调用，又因为每一个 Symbol 实例都是独一无二的，所以就不存在被误写的可能啦。

需要注意的是，不能用 `.` 运算符来定义或调用，而是要将 Symbol 实例放在方括号中。

### 遍历

当 Symbol 作为属性名的时候，如果我们对对象进行遍历，例如：

```JavaScript
var numKey = Symbol('Number')
var funKey = Symbol('Funtion')

var obj = {
    [numKey]: 1,
    [funKey]: function () {
        // do something
    },
    strKey: 'String'
}

for (item in obj) {
    item
}

// String
```

那些使用 Symbol 作为属性名的属性是不会出现在遍历循环中的，包括一些常用的遍历方法：`for...in`、`for...of`、`Object.keys()`、`Object.getOwnPropertyNames()` 和 `JSON.stringify()`。

但是这不代表这些属性是私有属性，我们可以用 `Object.getOwnPropertySymbols()` 方法来获取所有的 Symbol 属性名：

```JavaScript
var numKey = Symbol('Number')
var funKey = Symbol('Funtion')

var obj = {
    [numKey]: 1,
    [funKey]: function () {
        // do something
    },
    strKey: 'String'
}

Object.getOwnPropertySymbols(obj)
// [Symbol(Number), Symbol(Funtion)]
```

或者是使用一个新的 API：`Reflect.ownKeys()` 来返回所有的键名：

```JavaScript
var numKey = Symbol('Number')
var funKey = Symbol('Funtion')

var obj = {
    [numKey]: 1,
    [funKey]: function () {
        // do something
    },
    strKey: 'String'
}

Reflect.ownKeys(obj)
// ["strKey", Symbol(Number), Symbol(Funtion)]
```

## Symbol 怎么用

基本的用法之前已经说了，使用 `Symbol()` 就可以生成一个独一无二的 symbol。

### Symbol() 参数

`Symbol()` 可以传入一个字符串作为参数，表示对 Symbol 实例的描述。主要作用是为了调试和字符串打印输出的时候方便分辨：

```JavaScript
let s1 = Symbol('student')
let s2 = Symbol('teacher')

s1 // Symbol(student)
s2 // Symbol(teacher)

s1.toString() // "Symbol(student)"
s2.toString() // "Symbol(teacher)"
```

### Symbol.prototype.description

上面提到在生成 Symbol 实例的时候可以添加一个字符串参数作为其描述，如果需要读取这个值就可以用：

```JavaScript
let student = Symbol('student')

student.description // "student"
```

### Symbol.for()

`Symbol.for()` 可以通过传入一个字符串来全局寻找我们已经定义过的 Symbol 实例，如果没有找到则返回一个新的 Symbol 实例：

```JavaScript
let s1 = Symbol.for('foo')
let s2 = Symbol.for('foo')

s1 === s2 // true
```

`Symbol.for()` 和 `Symbol()` 虽然都会返回一个 Symbol 类型，但是不同的是，如果分别多次调用这两个方法，前者只在第一次返回一个新的 Symbol 实例，之后如果参数不改变的话，不管调用多少次都是会返回第一次返回的 Symbol 实例。而后者，每次调用都会生成一个全新的 Symbol 实例，调用多少次就生成多少个，不论参数是否改变。

### Symbol.keyFor()

`Symbol.keyFor()` 可以通过传入一个 Symbol 实例来获取它的 `key`：

```JavaScript
let s1 = Symbol.for("foo")
Symbol.keyFor(s1) // "foo"

let s2 = Symbol("foo");
Symbol.keyFor(s2) // undefined
```

需要注意的是，`Symbol.keyFor()` 只对通过 `Symbol.for()` 生成的 Symbol 实例有效。

## 参考

[Symbol - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Symbol)

[https://es6.ruanyifeng.com/#docs/symbol](https://es6.ruanyifeng.com/#docs/symbol)

[「每日一题」JS 中的 Symbol 是什么？](https://zhuanlan.zhihu.com/p/22652486)

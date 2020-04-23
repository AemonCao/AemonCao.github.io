---
title: Symbol 是什么？
tags:
  - JavaScript
  - ECMAScript 6
categories:
  - JavaScript
---

前些天偶然发现了 Symbol，学习了一番，这里记录一下。

<!-- more -->

##  Symbol 是什么？

symbol 是一种基本数据类型（primitive data type），它是在 ECMAScript 6 引入的一种新的基本数据类型，将 JavaScript 的基本数据类型扩展到了 7 种（分别是 string，number，bigint，boolean，null，undefined 和 symbol）。

##  Symbol 有什么用？

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
        break;
    case permissions.teacher:
        // do something
        break;
    case permissions.administrator:
        // do something
        break;
    default:
        // do something
        break;
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
var val = Symbol();
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

##  Symbol 怎么用？

基本的用法之前已经说了，使用 `Symbol()` 就可以生成一个独一无二的 symbol。

### Symbol() 参数

`Symbol()` 可以传入一个字符串作为参数，表示对 Symbol 实例的描述。主要作用是为了调试和字符串打印输出的时候方便分辨：

```JavaScript
let s1 = Symbol('student');
let s2 = Symbol('teacher');

s1 // Symbol(student)
s2 // Symbol(teacher)

s1.toString() // "Symbol(student)"
s2.toString() // "Symbol(teacher)"
```

### Symbol.prototype.description

上面提到在生成 Symbol 实例的时候可以添加一个字符串参数作为其描述，如果需要读取这个值就可以用：

```JavaScript
let student = Symbol('student');

student.description // "student"
```

##  参考

[Symbol - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Symbol)

[https://es6.ruanyifeng.com/#docs/symbol](https://es6.ruanyifeng.com/#docs/symbol)

[「每日一题」JS 中的 Symbol 是什么？](https://zhuanlan.zhihu.com/p/22652486)
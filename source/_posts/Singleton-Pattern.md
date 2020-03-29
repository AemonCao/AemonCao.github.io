---
title: 单例模式（Singleton Pattern）
date: 2020-03-29 20:25:58
tags:
  - 'C#'
  - 设计模式
  - 单例模式
  - 'Singleton Pattern'
categories:
  - 设计模式
---

单例模式的目的是：

> 确保类只有一个实例并提供全局访问。

<!-- more -->

##  实现

首先是最简单的单例模式的代码：

```csharp
namespace DesignPattern.SingletonPattern
{
    /// <summary>
    /// 单例
    /// </summary>
    public class Singleton
    {
        /// <summary>
        /// 记录唯一实例的静态变量
        /// </summary>
        private static Singleton uniqueInstance;

        /// <summary>
        /// 私有的构造函数
        /// </summary>
        private Singleton() { }

        /// <summary>
        /// 获取实例
        /// </summary>
        /// <returns>唯一的 Singleton 实例</returns>
        public static Singleton getInstance()
        {
            if (uniqueInstance == null)
                uniqueInstance = new Singleton();
            return uniqueInstance;
        }
    }
}
```

`Singleton` 的构造方法被定义成了 `private`，这代表无法在 `Singleton` 类的外部实例化 `Singleton` 类。

`getInstance` 方法被设置为 `static`，这样在 `Singleton` 类外部即使不实例化 `Singleton` 类也可以通过 `Singleton.getInstance()` 调用此方法。

这样，只要在 `getInstance` 方法中进行简单的判断，就可以实现从始至终只有一个 `Singleton` 类的实例。

##  多线程

然而，在多线程时就会出现问题：两个线程在运行时，可能会同时出现 `(uniqueInstance == null)` 返回为真的情况，这就导致会实例化一个以上的 `Singleton` 类的情况出现。

所以，我们需要将其变成同步方法，在 C# 中可以用以下两种方法实现：

第一是使用 `[MethodImpl(MethodImplOptions.Synchronized)]` 属性：

```csharp
using System.Runtime.CompilerServices;

namespace DesignPattern.SingletonPattern
{
    /// <summary>
    /// 单例
    /// </summary>
    public class Singleton
    {
        /// <summary>
        /// 记录唯一实例的静态变量
        /// </summary>
        private static Singleton uniqueInstance;

        /// <summary>
        /// 私有的构造函数
        /// </summary>
        private Singleton() { }

        /// <summary>
        /// 获取实例
        /// </summary>
        /// <returns>唯一的 Singleton 实例</returns>
        [MethodImpl(MethodImplOptions.Synchronized)]
        public static Singleton getInstance()
        {
            if (uniqueInstance == null)
                uniqueInstance = new Singleton();
            return uniqueInstance;
        }
    }
}
```

第二种是使用 `lock` ：

```csharp
namespace DesignPattern.SingletonPattern
{
    /// <summary>
    /// 单例
    /// </summary>
    public class Singleton
    {
        /// <summary>
        /// 记录唯一实例的静态变量
        /// </summary>
        private static Singleton uniqueInstance;

        /// <summary>
        /// lock 标识
        /// </summary>
        private static readonly object locker = new object();

        /// <summary>
        /// 私有的构造函数
        /// </summary>
        private Singleton() { }

        /// <summary>
        /// 获取实例
        /// </summary>
        /// <returns>唯一的 Singleton 实例</returns>
        public static Singleton getInstance()
        {
            lock (locker)
            {
                if (uniqueInstance == null)
                    uniqueInstance = new Singleton();
            }
            return uniqueInstance;
        }
    }
}
```

上面两种方法都可以解决在单例模式下多线程的问题，但是如果你在程序中频繁用到 `Singleton.getInstance()` 将会大大的使性能降低，因为同步一个方法会使程序执行效率下降，所以我们可以在这种情况下使用如下方法：

```csharp
namespace DesignPattern.SingletonPattern
{
    /// <summary>
    /// 单例
    /// </summary>
    public class Singleton
    {
        /// <summary>
        /// 记录唯一实例的静态变量
        /// </summary>
        private static Singleton uniqueInstance = new Singleton();

        /// <summary>
        /// 私有的构造函数
        /// </summary>
        private Singleton() { }

        /// <summary>
        /// 获取实例
        /// </summary>
        /// <returns>唯一的 Singleton 实例</returns>
        public static Singleton getInstance()
        {
            return uniqueInstance;
        }
    }
}
```

可以看到，我们在声明 `uniqueInstance` 的时候直接实例化了 `Singleton` 类。

这样在程序开始的时候这个唯一实例就已经存在了。避免了同步方法，也就解决了多线程的问题。

##  注意的点

* 在多线程问题的解决上，需要选择适合的方案来实现单例模式，如果单例实例在程序中并不是经常使用，而且实例化的时候开销很大的话，可以使用同步 `getInstance` 方法来解决多线程问题，如果单例实例需要在程序中频繁使用，那就可以在声明 `uniqueInstance` 变量的时间就进行实例化，这样就可以解决同步 `getInstance` 方法时带来的性能下降的问题。

* 如果你有多个构造函数（重载），就需要注意会产生多个实例导致单例失效。

##  代码

[SingletonPattern](https://github.com/AemonCao/DesignPattern/tree/master/DesignPattern/SingletonPattern)

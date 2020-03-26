---
title: 设计模式
tags: 
  - C#
  - 设计模式
---

说来惭愧，一直没有好好学这部分的内容，所以趁着这次机会把设计模式这块的知识好好学一下。

我会使用 C# 来写一些实例代码，并且会将代码放在 [GitHub](https://github.com/AemonCao/DesignPattern) 上面，以供参考。

<!-- more -->

##  什么是设计模式

> 在软件工程中，设计模式（design pattern）是对软件设计中普遍存在（反复出现）的各种问题，所提出的解决方案。这个术语是由埃里希·伽玛（Erich Gamma）等人在1990年代从建筑设计领域引入到计算机科学的。

先暂时将 wiki 上的说明拿过来用一下，我相信等我学完之后，可以根据自己的理解再来写一下这个问题：「什么是设计模式？」

##  单例模式（Singleton Pattern）

### 目的

单例模式的目的是：

> 确保类只有一个实例并提供全局访问。

### 实现

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

### 多线程

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

### 注意的点

* 在多线程问题的解决上，需要选择适合的方案来实现单例模式，如果单例实例在程序中并不是经常使用，而且实例化的时候开销很大的话，可以使用同步 `getInstance` 方法来解决多线程问题，如果单例实例需要在程序中频繁使用，那就可以在声明 `uniqueInstance` 变量的时间就进行实例化，这样就可以解决同步 `getInstance` 方法时带来的性能下降的问题。

* 如果你有多个构造函数（重载），就需要注意会产生多个实例导致单例失效。
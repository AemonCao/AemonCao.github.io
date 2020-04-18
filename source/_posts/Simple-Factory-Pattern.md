---
title: 简单工厂模式（Simple Factory Pattern）
date: 2020-03-29 20:27:46
tags:
  - 'C#'
  - 设计模式
  - 简单工厂模式
  - 'Simple Factory Pattern'
categories:
  - 设计模式
---

简单工厂模式的目的是：

> 使用一个类来生产对象。

<!-- more -->

##  实现

以订购披萨为例。

首先我们需要一些披萨：

```csharp
using System;

namespace DesignPattern.SimpleFactoryPattern
{
    /// <summary>
    /// 披萨抽象类
    /// </summary>
    public abstract class Pizza
    {
        /// <summary>
        /// 准备 Pizza
        /// </summary>
        public abstract void Prepare();

        /// <summary>
        /// 烘培 Pizza
        /// </summary>
        public abstract void Bake();

        /// <summary>
        /// 切 Pizza
        /// </summary>
        public abstract void Cut();

        /// <summary>
        /// 装箱 Pizza
        /// </summary>
        public abstract void Box();
    }

    /// <summary>
    /// 芝士披萨
    /// </summary>
    public class CheesePizza : Pizza
    {
        public CheesePizza()
        {
            Console.WriteLine("芝士披萨");
        }

        public override void Prepare()
        {
            Console.WriteLine("准备芝士披萨!");
        }

        public override void Bake()
        {
            Console.WriteLine("烘焙芝士披萨!");
        }

        public override void Cut()
        {
            Console.WriteLine("切芝士披萨!");
        }

        public override void Box()
        {
            Console.WriteLine("装箱芝士披萨!");
        }
    }

    /// <summary>
    /// 蛤披萨
    /// </summary>
    public class ClamPizza : Pizza
    {
        public ClamPizza()
        {
            Console.WriteLine("蛤披萨");
        }

        public override void Prepare()
        {
            Console.WriteLine("准备蛤披萨!");
        }

        public override void Bake()
        {
            Console.WriteLine("烘焙蛤披萨!");
        }

        public override void Cut()
        {
            Console.WriteLine("切蛤披萨!");
        }

        public override void Box()
        {
            Console.WriteLine("装箱蛤披萨!");
        }
    }

    /// <summary>
    /// 意大利辣香肠披萨
    /// </summary>
    public class PepperoniPizza : Pizza
    {
        public PepperoniPizza()
        {
            Console.WriteLine("意大利辣香肠披萨");
        }
        public override void Prepare()
        {
            Console.WriteLine("准备意大利辣香肠披萨!");
        }

        public override void Bake()
        {
            Console.WriteLine("烘焙意大利辣香肠披萨!");
        }

        public override void Cut()
        {
            Console.WriteLine("切意大利辣香肠披萨!");
        }

        public override void Box()
        {
            Console.WriteLine("装箱意大利辣香肠披萨!");
        }
    }

    /// <summary>
    /// 素食披萨
    /// </summary>
    public class VeggiePizza : Pizza
    {
        public VeggiePizza()
        {
            Console.WriteLine("素食披萨");
        }

        public override void Prepare()
        {
            Console.WriteLine("准备素食披萨!");
        }

        public override void Bake()
        {
            Console.WriteLine("烘焙素食披萨!");
        }

        public override void Cut()
        {
            Console.WriteLine("切素食披萨!");
        }

        public override void Box()
        {
            Console.WriteLine("装箱素食披萨!");
        }
    }
}
```

然后我们需要一个工厂（Factory）来生产披萨：

```csharp
using System;

namespace DesignPattern.SimpleFactoryPattern
{
    public class SimplePizzaFactory
    {
        public Pizza CreatePizza(String type)
        {
            Pizza pizza = null;
            if (type.Equals("cheese"))
                pizza = new CheesePizza();
            else if (type.Equals("pepperoni"))
                pizza = new PepperoniPizza();
            else if (type.Equals("clam"))
                pizza = new ClamPizza();
            else if (type.Equals("veggie"))
                pizza = new VeggiePizza();

            return pizza;
        }
    }
}
```

这样，我们就可以订购披萨啦：

```csharp
namespace DesignPattern.SimpleFactoryPattern
{
    class PizzaStore
    {
        SimplePizzaFactory factory;

        public PizzaStore(SimplePizzaFactory factory)
        {
            this.factory = factory;
        }

        public Pizza OrderPizza(string type)
        {
            Pizza pizza;

            pizza = factory.CreatePizza(type);

            pizza.Prepare();
            pizza.Bake();
            pizza.Cut();
            pizza.Box();

            return pizza;
        }
    }
}
```

可以看到，在 `PizzaStore` 中，我们实例化了一个 `SimplePizzaFactory`。之后在 `OrderPizza` 中当我们需要一个 `Pizza` 的时候，我们是通过工厂的 `CreatePizza` 方法来创建传入 `type` 相对应的 `Pizza`。

这就是一个最简单的简单工厂模式的实现，我们可以看到，在 `PizzaStore` 中，并非是通过 `new` 来获取一个对象，而是将实例化的工作交给了 `SimplePizzaFactory`，而这就是工厂类的用途。

这样做有什么好处呢？

在这个例子里，我们只有一个 `PizzaStore` 使用了工厂来创建披萨。但是披萨店可不止有一家，当我们做大做强，拥有了更多的连锁店的时候，我们就能体会到 `SimplePizzaFactory` 的好处了，如果我们需要升级一下披萨的制作工艺，我们不必对每个门店进行改造，而是只对工厂进行升级，这样所有使用 `SimplePizzaFactory` 的披萨店都可以享受到最新的工艺做出来的披萨啦。

##  注意的点

* 如果将工厂定义为静态方法，这么做的好处是不必再创建对象的方法来实例化对象，但是缺点是无法通过继承来改变创建方法的行为。

##  代码

[SimpleFactoryPattern](https://github.com/AemonCao/DesignPattern/tree/master/DesignPattern/SimpleFactoryPattern)

##  P.S.

这部分的代码来自《Head First 设计模式》。

话说好久没吃披萨了，写得我好饿。

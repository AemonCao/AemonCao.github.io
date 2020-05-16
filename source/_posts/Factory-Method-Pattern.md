---
title: 工厂方法模式（Factory Method Pattern）
tags:
  - 'C#'
  - 设计模式
  - 工厂方法模式
  - Factory Method Pattern
categories:
  - 设计模式
date: 2020-03-31 22:55:39
---

工厂方法模式的目的是：

> 通过让子类决定该创建的对象是什么，来达到将对象创建的过程封装的目的。

<!-- more -->

## 实现

我们还是继续{% post_link Simple-Factory-Pattern '简单工厂模式（Simple Factory Pattern）' %}中的披萨店。

首先我们需要修改一下 `Pizza` 类：

```csharp
/// <summary>
/// 披萨抽象类
/// </summary>
public abstract class Pizza
{
    public string name;
    public string dough;
    public string sauce;
    public ArrayList toppings = new ArrayList();

    /// <summary>
    /// 准备 Pizza
    /// </summary>
    public virtual void Prepare()
    {
        Console.WriteLine("Prepring " + name);
        Console.WriteLine("Tossing dough...");
        Console.WriteLine("Adding sauce...");
        Console.WriteLine("Adding toppings: ");
        for (int i = 0; i < toppings.Count; i++)
            Console.WriteLine("    " + toppings[i]);
    }

    /// <summary>
    /// 烘培 Pizza
    /// </summary>
    public virtual void Bake()
    {
        Console.WriteLine("Bake for 25 minutes at 350");
    }

    /// <summary>
    /// 切 Pizza
    /// </summary>
    public virtual void Cut()
    {
        Console.WriteLine("Cutting the pizza into diagonal slices");
    }

    /// <summary>
    /// 装箱 Pizza
    /// </summary>
    public virtual void Box()
    {
        Console.WriteLine("Place pizza in official PizzaStore box");
    }

    public String getName()
    {
        return name;
    }
}
```

然后通过继承，我们有了几种不同风格和口味的 `Pizza`：

```csharp
/// <summary>
/// 纽约芝士披萨
/// </summary>
public class NYStyleCheesePizza : Pizza
{
    public NYStyleCheesePizza()
    {
        name = "NY Style Sauce and Cheese Pizza";
        dough = "Thin Crust Dough";
        sauce = "Marinara Sauce";

        toppings.Add("Grated Reggiano Cheese");
    }
}

/// <summary>
/// 纽约素食披萨
/// </summary>
public class NYStyleVeggiePizza : Pizza
{
    public NYStyleVeggiePizza()
    {
        name = "NY Style Sauce and Veggie Pizza";
        dough = "Thin Crust Dough";
        sauce = "Marinara Sauce";

        toppings.Add("Grated Reggiano Veggie");
    }
}

/// <summary>
/// 芝加哥芝士披萨
/// </summary>
public class ChicagoStyleCheesePizza : Pizza
{
    public ChicagoStyleCheesePizza()
    {
        name = "Chicago Style Deep Dish Cheese Pizza";
        dough = "Extra Thick Crust Dough";
        sauce = "Plum Tomato Sauce";

        toppings.Add("Shredded Mozzarella Cheese");
    }

    public override void Cut()
    {
        Console.WriteLine("Cutting the pizza into square slices");
    }
}

/// <summary>
/// 芝加哥素食披萨
/// </summary>
public class ChicagoStyleVeggiePizza : Pizza
{
    public ChicagoStyleVeggiePizza()
    {
        name = "Chicago Style Deep Dish Veggie Pizza";
        dough = "Extra Thick Crust Dough";
        sauce = "Plum Tomato Sauce";

        toppings.Add("Shredded Mozzarella Veggie");
    }

    public override void Cut()
    {
        Console.WriteLine("Cutting the pizza into square slices");
    }
}
```

相比较之前的写法，新的写法将 `Pizza` 抽象类中的方法改成了虚方法（`virtual`）。这样的话，之后继承此抽象类的子类就不需要某些不用改动的方法进行重复的 `override` 了。就像代码所展示的一样，我们只对 `Cut` 方法进行了覆写，因为 Chicago Style 的披萨需要切成方形的。

之后是 `PizzaStore` 的部分：

```csharp
public abstract class PizzaStore
{
    protected abstract Pizza CreatePizza(string type);

    public Pizza OrderPizza(string type)
    {
        Pizza pizza;

        pizza = CreatePizza(type);

        pizza.Prepare();
        pizza.Bake();
        pizza.Cut();
        pizza.Box();

        return pizza;
    }
}
```

我们将 `PizzaStore` 也写成了一个抽象类，我们把它称作**抽象创造者类**，同时还有一个 `CreatePizza` 的抽象方法。而这就是**工厂方法**，当然，是抽象的。它返回一个 `Pizza`，这就是**产品**。

继承：

```csharp
public class NYStylePizzaStore : PizzaStore
{
    protected override Pizza CreatePizza(string type)
    {
        if (type.Equals("cheese"))
            return new NYStyleCheesePizza();
        else if (type.Equals("viggie"))
            return new NYStyleVeggiePizza();
        else
            return null;
    }
}

public class ChicagoStylePizzaStore : PizzaStore
{
    protected override Pizza CreatePizza(string type)
    {
        if (type.Equals("cheese"))
            return new ChicagoStyleCheesePizza();
        else if (type.Equals("viggie"))
            return new ChicagoStyleVeggiePizza();
        else
            return null;
    }
}
```

这两个 `PizzaStore` 的子类（**具体创造者**）实现了 `CreatePizza` 抽象方法，这就是真正实现实例化的工厂方法。

我们把实例化的工作都交给了子类，或者说是交给了子类中的一个方法，这个方法就是一个工厂。

## 注意的点

* 简单工厂和工厂方法之间的差异：简单方法把全部的事情都在一个地方做完了，而工厂方法则是定义了一个框架，通过继承抽象类来创造实际的产品。

## 代码

[FactoryMethodPattern](https://github.com/AemonCao/DesignPattern/tree/master/DesignPattern/FactoryMethodPattern)

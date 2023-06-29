---
title: 抽象工厂模式（Abstract Factory Pattern）
tags:
  - 'C#'
  - 设计模式
  - 抽象工厂模式
  - Abstract Factory Pattern
categories:
  - 设计模式
date: 2020-04-18 18:22:47
---


抽象工厂模式的定义是：

> 提供一个接口，用于创建相关或依赖对象的家族，而不需要明确指定具体类。

<!-- more -->

## 实现

上一篇{% post_link Factory-Method-Pattern '工厂方法模式（Factory Method Pattern）' %}中，我们已经能生产不同区域的不同披萨了，但是每家店使用的原料都是不一致的，做为一个好的连锁店，我们需要确保原料的高质量。这里需要注意的是，即使是同一种披萨，在不同的区域使用的原料也是会有区别的，会根据当地人的口味进行调整，那么为了保证以上两点，我们需要建立原料的工厂。

首先，即使原料略有不同，但是原料的种类就是那么几种：面团（Dough）、酱（Sauce）、起司（Cheese）、蔬菜（Veggies）、意大利辣香肠（Pepperoni）和蛤（Clam）。

大多数的披萨，都是由上面这些原料组成的，所以我们的原料工厂要保证能生产这些原料：

```csharp
/// <summary>
/// 披萨原料工厂
/// </summary>
public interface PizzaIngredientFactory
{
    public IDough createDough();
    public ISauce createSauce();
    public ICheese createCheese();
    public IVeggies[] createVeggies();
    public IPepperoni createPepperoni();
    public IClams createClam();
}
```

每种原料下面又分成好多种不同地区的原料，以面团为例：

```csharp
namespace DesignPattern.AbstractFactoryPattern
{
    /// <summary>
    /// 面团
    /// </summary>
    public interface IDough
    {
        public string ToString();
    }

    /// <summary>
    /// 厚皮面团
    /// </summary>
    public class ThickCrustDough : IDough
    {
        public override string ToString()
        {
            return "厚皮面团";
        }
    }

    /// <summary>
    /// 薄皮面团
    /// </summary>
    public class ThinCrustDough : IDough
    {
        public override string ToString()
        {
            return "薄皮面团";
        }
    }
}
```

其他种类的原料也是类似：

```csharp
namespace DesignPattern.AbstractFactoryPattern
{
    /// <summary>
    /// 酱
    /// </summary>
    public interface ISauce
    {
        public string ToString();
    }

    /// <summary>
    /// 意大利西红柿酱
    /// </summary>
    public class MarinaraSauce : ISauce
    {
        public string ToString()
        {
            return "意大利西红柿酱";
        }
    }

    /// <summary>
    /// 梅子番茄酱
    /// </summary>
    public class PlumTomatoSauce : ISauce
    {
        public string ToString()
        {
            return "梅子西红柿酱";
        }
    }
}
```

```csharp
namespace DesignPattern.AbstractFactoryPattern
{
    /// <summary>
    /// 起司
    /// </summary>
    public interface ICheese
    {
        public string ToString();
    }

    /// <summary>
    /// 芝士丝
    /// </summary>
    public class MozzarellaCheese : ICheese
    {
        public string ToString()
        {
            return "芝士丝";
        }
    }

    /// <summary>
    /// 帕尔马干酪
    /// </summary>
    public class ParmesanCheese : ICheese
    {
        public string ToString()
        {
            return "帕尔马干酪";
        }
    }

    /// <summary>
    /// 雷吉亚诺奶酪
    /// </summary>
    public class ReggianoCheese : ICheese
    {
        public string ToString()
        {
            return "雷吉亚诺奶酪";
        }
    }
}
```

```csharp
namespace DesignPattern.AbstractFactoryPattern
{
    /// <summary>
    /// 蔬菜
    /// </summary>
    public interface IVeggies
    {
        public string ToString();
    }

    /// <summary>
    /// 茄子
    /// </summary>
    public class Eggplant : IVeggies
    {
        public override string ToString()
        {
            return "茄子";
        }
    }

    /// <summary>
    /// 大蒜
    /// </summary>
    public class Garlic : IVeggies
    {
        public override string ToString()
        {
            return "大蒜";
        }
    }

    /// <summary>
    /// 菠菜
    /// </summary>
    public class Spinach : IVeggies
    {
        public override string ToString()
        {
            return "菠菜";
        }
    }

    /// <summary>
    /// 洋葱
    /// </summary>
    public class Onion : IVeggies
    {
        public override string ToString()
        {
            return "洋葱";
        }
    }

    /// <summary>
    /// 蘑菇
    /// </summary>
    public class Mushroom : IVeggies
    {
        public override string ToString()
        {
            return "蘑菇";
        }
    }

    /// <summary>
    /// 辣椒
    /// </summary>
    public class RedPepper : IVeggies
    {
        public override string ToString()
        {
            return "辣椒";
        }
    }

    /// <summary>
    /// 黑橄榄
    /// </summary>
    public class BlackOlives : IVeggies
    {
        public override string ToString()
        {
            return "黑橄榄";
        }
    }
}
```

```csharp
namespace DesignPattern.AbstractFactoryPattern
{
    /// <summary>
    /// 意大利辣香肠
    /// </summary>
    public interface IPepperoni
    {
        public string ToString();
    }

    public class SlicedPepperoni : IPepperoni
    {
        public override String ToString()
        {
            return "切意大利辣香肠";
        }
    }
}
```

```csharp
namespace DesignPattern.AbstractFactoryPattern
{
    /// <summary>
    /// 蛤
    /// </summary>
    public interface IClams
    {
        public string ToString();
    }

    /// <summary>
    /// 新鲜的蛤
    /// </summary>
    public class FreshClams : IClams
    {
        public override String ToString()
        {
            return "长岛之声的新鲜蛤";
        }
    }

    /// <summary>
    /// 冷冻的蛤
    /// </summary>
    public class FrozenClams : IClams
    {
        public override String ToString()
        {
            return "切萨皮克湾的冷冻蛤";
        }
    }
}
```

接下来，我们就可以根据原料工厂来创建不同地区的原料工厂了，以纽约原料工厂为例：

```csharp
/// <summary>
/// 纽约披萨原料工厂
/// </summary>
public class NYPizzaIngredientFactory : PizzaIngredientFactory
{
    public IDough createDough()
    {
        return new ThinCrustDough();
    }

    public ISauce createSauce()
    {
        return new MarinaraSauce();
    }

    public ICheese createCheese()
    {
        return new ReggianoCheese();
    }

    public IVeggies[] createVeggies()
    {
        IVeggies[] veggies = {
        new Garlic(),
        new Onion(),
        new Mushroom(),
        new RedPepper()
    };
        return veggies;
    }

    public IPepperoni createPepperoni()
    {
        return new SlicedPepperoni();
    }

    public IClams createClam()
    {
        return new FreshClams();
    }
}
```

同样的，芝加哥原料工厂如下：

```csharp
/// <summary>
/// 芝加哥披萨原料工厂
/// </summary>
public class ChicagoPizzaIngredientFactory : PizzaIngredientFactory
{
    public IDough createDough()
    {
        return new ThickCrustDough();
    }

    public ISauce createSauce()
    {
        return new PlumTomatoSauce();
    }

    public ICheese createCheese()
    {
        return new MozzarellaCheese();
    }

    public IVeggies[] createVeggies()
    {
        IVeggies[] veggies = {
            new BlackOlives(),
            new Spinach(),
            new Eggplant()
        };
        return veggies;
    }

    public IPepperoni createPepperoni()
    {
        return new SlicedPepperoni();
    }

    public IClams createClam()
    {
        return new FrozenClams();
    }
}
```

现在，所有的原料已经准备完成，我们需要修改一下披萨，使其只能使用我们原料工厂提供的原料：

```csharp
public abstract class Pizza
{
    public String name;

    public IDough dough;
    public ISauce sauce;
    public IVeggies[] veggies;
    public ICheese cheese;
    public IPepperoni pepperoni;
    public IClams clam;

    /// <summary>
    /// 准备
    /// </summary>
    public abstract void prepare();

    /// <summary>
    /// 烘焙
    /// </summary>
    public void bake()
    {
        Console.WriteLine("350摄氏度烘焙25分钟。");
    }

    /// <summary>
    /// 切
    /// </summary>
    public void cut()
    {
        Console.WriteLine("将披萨切成对角。");
    }

    /// <summary>
    /// 装盒
    /// </summary>
    public void box()
    {
        Console.WriteLine("将披萨放入盒中");
    }

    public void setName(String name)
    {
        this.name = name;
    }

    public String getName()
    {
        return name;
    }

    public override String ToString()
    {
        StringBuilder result = new StringBuilder();
        result.Append("---- " + name + " ----\n");
        if (dough != null)
        {
            result.Append(dough);
            result.Append("\n");
        }
        if (sauce != null)
        {
            result.Append(sauce);
            result.Append("\n");
        }
        if (cheese != null)
        {
            result.Append(cheese);
            result.Append("\n");
        }
        if (veggies != null)
        {
            for (int i = 0; i < veggies.Length; i++)
            {
                result.Append(veggies[i]);
                if (i < veggies.Length - 1)
                {
                    result.Append(", ");
                }
            }
            result.Append("\n");
        }
        if (clam != null)
        {
            result.Append(clam);
            result.Append("\n");
        }
        if (pepperoni != null)
        {
            result.Append(pepperoni);
            result.Append("\n");
        }
        return result.ToString();
    }
}
```

在抽象类 `Pizza` 中，`prepare` 方法也被定义为抽象方法，因为这里我们需要根据不同的披萨生成不同的原料。

接下来，我们就可以制作披萨了：

```csharp
/// <summary>
/// 芝士披萨
/// </summary>
public class CheesePizza : Pizza
{
    PizzaIngredientFactory ingredientFactory;

    public CheesePizza(PizzaIngredientFactory ingredientFactory)
    {
        this.ingredientFactory = ingredientFactory;
    }

    public override void prepare()
    {
        Console.WriteLine("准备：" + name);
        dough = ingredientFactory.createDough();
        sauce = ingredientFactory.createSauce();
        cheese = ingredientFactory.createCheese();
    }
}
```

在重写的 `prepare` 方法中，当我们需要什么原料我们只要向原料工厂要就行了。具体是什么原料，由原料工厂来决定，纽约的原料工厂就给纽约风格的原料，芝加哥的原料工厂就给芝加哥风格的原料。

同样的，蛤蜊披萨也是：

```csharp
/// <summary>
/// 蛤蜊披萨
/// </summary>
public class ClamPizza : Pizza
{
    PizzaIngredientFactory ingredientFactory;

    public ClamPizza(PizzaIngredientFactory ingredientFactory)
    {
        this.ingredientFactory = ingredientFactory;
    }

    public override void prepare()
    {
        Console.WriteLine("准备：" + name);
        dough = ingredientFactory.createDough();
        sauce = ingredientFactory.createSauce();
        cheese = ingredientFactory.createCheese();
        clam = ingredientFactory.createClam();
    }
}
```

相对于 `CheesePizza`，`ClamPizza` 多了蛤蜊作为原谅，至于蛤蜊的种类，就由所在地的工厂来决定。如果是靠海的纽约，那就可以拿到新鲜的蛤蜊，如果是内陆的芝加哥，那么就只能使用冷冻的蛤蜊了。

另外两种披萨：

```csharp
/// <summary>
/// 素食披萨
/// </summary>
public class VeggiePizza : Pizza
{
    PizzaIngredientFactory ingredientFactory;

    public VeggiePizza(PizzaIngredientFactory ingredientFactory)
    {
        this.ingredientFactory = ingredientFactory;
    }

    public override void prepare()
    {
        Console.WriteLine("准备：" + name);
        dough = ingredientFactory.createDough();
        sauce = ingredientFactory.createSauce();
        cheese = ingredientFactory.createCheese();
        veggies = ingredientFactory.createVeggies();
    }
}
```

```csharp
/// <summary>
/// 意大利辣香肠比萨
/// </summary>
public class PepperoniPizza : Pizza
{
    PizzaIngredientFactory ingredientFactory;

    public PepperoniPizza(PizzaIngredientFactory ingredientFactory)
    {
        this.ingredientFactory = ingredientFactory;
    }

    public override void prepare()
    {
        Console.WriteLine("准备：" + name);
        dough = ingredientFactory.createDough();
        sauce = ingredientFactory.createSauce();
        cheese = ingredientFactory.createCheese();
        veggies = ingredientFactory.createVeggies();
        pepperoni = ingredientFactory.createPepperoni();
    }
}
```

再回到披萨店：

```csharp
public abstract class PizzaStore
{
    protected abstract Pizza createPizza(String item);

    public Pizza orderPizza(String type)
    {
        Pizza pizza = createPizza(type);

        Console.WriteLine("制作一个" + pizza.getName());

        pizza.prepare();
        pizza.bake();
        pizza.cut();
        pizza.box();

        return pizza;
    }
}
```

创建纽约披萨加盟店和芝加哥披萨加盟店：

```csharp
/// <summary>
/// 纽约披萨店
/// </summary>
public class NYPizzaStore : PizzaStore
{
    protected override Pizza createPizza(string item)
    {
        Pizza pizza = null;
        PizzaIngredientFactory ingredientFactory = new NYPizzaIngredientFactory();

        if (item.Equals("cheese"))
        {
            pizza = new CheesePizza(ingredientFactory);
            pizza.setName("纽约风格芝士披萨");
        }
        else if (item.Equals("veggie"))
        {
            pizza = new VeggiePizza(ingredientFactory);
            pizza.setName("纽约风格素食披萨");
        }
        else if (item.Equals("clam"))
        {
            pizza = new ClamPizza(ingredientFactory);
            pizza.setName("纽约风格蛤蜊披萨");
        }
        else if (item.Equals("pepperoni"))
        {
            pizza = new PepperoniPizza(ingredientFactory);
            pizza.setName("纽约风格意大利辣香肠披萨");
        }

        return pizza;
    }
}
```

```csharp
/// <summary>
/// 芝加哥披萨店
/// </summary>
public class ChicagoPizzaStore : PizzaStore
{
    protected override Pizza createPizza(string item)
    {
        Pizza pizza = null;
        PizzaIngredientFactory ingredientFactory = new ChicagoPizzaIngredientFactory();

        if (item.Equals("cheese"))
        {
            pizza = new CheesePizza(ingredientFactory);
            pizza.setName("芝加哥风格芝士披萨");
        }
        else if (item.Equals("veggie"))
        {
            pizza = new VeggiePizza(ingredientFactory);
            pizza.setName("芝加哥风格素食披萨");
        }
        else if (item.Equals("clam"))
        {
            pizza = new ClamPizza(ingredientFactory);
            pizza.setName("芝加哥风格蛤蜊披萨");
        }
        else if (item.Equals("pepperoni"))
        {
            pizza = new PepperoniPizza(ingredientFactory);
            pizza.setName("芝加哥风格意大利辣香肠披萨");
        }

        return pizza;
    }
}
```

这两家披萨店就是抽象工厂的客户，如果想生产纽约风格的披萨，只需要使用纽约披萨原料工厂提供的原料。

## 注意的点

* 抽象工厂定义了一个接口，所有的具体工厂都必须实现此接口。

* 在客户的代码中，只需要涉及抽象工厂，当运行时将自动使用实际的具体工厂。

* 当需要创建产品家族和想让制造的相关产品集合起来时，可以使用抽象工厂，而当目前还不知道将来需要实例化哪些具体类时，就可以使用工厂方法。

## 代码

[AbstractFactoryPattern](https://github.com/AemonCao/DesignPattern/tree/master/DesignPattern/AbstractFactoryPattern)

## 最后

最后让我们点个披萨吃吧：

```csharp
PizzaStore nyStore = new NYPizzaStore();
Pizza pizza = nyStore.orderPizza("cheese");
```

---
title: 【转载】依赖注入那些事儿
tags:
  - 'C#'
  - 依赖注入
  - 转载
url: 227.html
id: 227
categories:
  - 'C#'
  - 转载
date: 2017-09-15 19:37:09
---

转载自：[依赖注入那些事儿 - T2噬菌体](http://www.cnblogs.com/leoo2sk/archive/2009/06/17/1504693.html)

<!-- more -->

##  IGame 游戏公司的故事

### 讨论会

话说有一个叫 IGame 的游戏公司，正在开发一款 ARPG 游戏（动作&角色扮演类游戏，如魔兽世界、梦幻西游这一类的游戏）。一般这类游戏都有一个基本的功能，就是打怪（玩家攻击怪物，借此获得经验、虚拟货币和虚拟装备），并且根据玩家角色所装备的武器不同，攻击效果也不同。这天，IGame 公司的开发小组正在开会对打怪功能中的某一个功能点如何实现进行讨论，他们面前的大屏幕上是这样一份需求描述的 PPT：

<!-- ![01_3.gif](https://i.loli.net/2017/09/15/59bb9d3e3a3c1.gif) -->
{% asset_img 1.webp PPT %}

**图1.1 需求描述ppt**

各个开发人员，面对这份需求，展开了热烈的讨论，下面我们看看讨论会上都发生了什么。

### 实习生小李的实现方式

在经过一番讨论后，项目组长 Peter 觉得有必要整理一下各方的意见，他首先询问小李的看法。小李是某学校计算机系大三学生，对游戏开发特别感兴趣，目前是 IGame 公司的一名实习生。 经过短暂的思考，小李阐述了自己的意见： “我认为，这个需求可以这么实现。HP当然是怪物的一个属性成员，而武器是角色的一个属性成员，类型可以使字符串，用于描述目前角色所装备的武器。角色类有一个攻击方法，以被攻击怪物为参数，当实施一次攻击时，攻击方法被调用，而这个方法首先判断当前角色装备了什么武器，然后据此对被攻击怪物的 `HP` 进行操作，以产生不同效果。” 而在阐述完后，小李也飞快的在自己的电脑上写了一个 Demo，来演示他的想法，Demo 代码如下：

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace IGameLi
{
    /// <summary>
    /// 怪物
    /// </summary>
    internal sealed class Monster
    {
        /// <summary>
        /// 怪物的名字
        /// </summary>
        public String Name { get; set; }

        /// <summary>
        /// 怪物的生命值
        /// </summary>
        public Int32 HP { get; set; }

        public Monster(String name,Int32 hp)
        {
            this.Name = name;
            this.HP = hp;
        }
    }
}
```

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace IGameLi
{
    /// <summary>
    /// 角色
    /// </summary>
    internal sealed class Role
    {
        private Random _random = new Random();

        /// <summary>
        /// 表示角色目前所持武器的字符串
        /// </summary>
        public String WeaponTag { get; set; }

        /// <summary>
        /// 攻击怪物
        /// </summary>
        /// <param name="monster">被攻击的怪物</param>
        public void Attack(Monster monster)
        {
            if (monster.HP <= 0)
            {
                Console.WriteLine("此怪物已死");
                return;
            }

            if ("WoodSword" == this.WeaponTag)
            {
                monster.HP -= 20;
                if (monster.HP <= 0)
                {
                    Console.WriteLine("攻击成功！怪物" + monster.Name + "已死亡");
                }
                else
                {
                    Console.WriteLine("攻击成功！怪物" + monster.Name + "损失20HP");
                }
            }
            else if ("IronSword" == this.WeaponTag)
            {
                monster.HP -= 50;
                if (monster.HP <= 0)
                {
                    Console.WriteLine("攻击成功！怪物" + monster.Name + "已死亡");
                }
                else
                {
                    Console.WriteLine("攻击成功！怪物" + monster.Name + "损失50HP");
                }
            }
            else if ("MagicSword" == this.WeaponTag)
            {
                Int32 loss = (_random.NextDouble() < 0.5) ? 100 : 200;
                monster.HP -= loss;
                if (200 == loss)
                {
                    Console.WriteLine("出现暴击！！！");
                }

                if (monster.HP <= 0)
                {
                    Console.WriteLine("攻击成功！怪物" + monster.Name + "已死亡");
                }
                else
                {
                    Console.WriteLine("攻击成功！怪物" + monster.Name + "损失" + loss + "HP");
                }
            }
            else
            {
                Console.WriteLine("角色手里没有武器，无法攻击！");
            }
        }
    }
}
```

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace IGameLi
{
    class Program
    {
        static void Main(string[] args)
        {
            //生成怪物
            Monster monster1 = new Monster("小怪A", 50);
            Monster monster2 = new Monster("小怪B", 50);
            Monster monster3 = new Monster("关主", 200);
            Monster monster4 = new Monster("最终Boss", 1000);

            //生成角色
            Role role = new Role();

            //木剑攻击
            role.WeaponTag = "WoodSword";
            role.Attack(monster1);

            //铁剑攻击
            role.WeaponTag = "IronSword";
            role.Attack(monster2);
            role.Attack(monster3);

            //魔剑攻击
            role.WeaponTag = "MagicSword";
            role.Attack(monster3);
            role.Attack(monster4);
            role.Attack(monster4);
            role.Attack(monster4);
            role.Attack(monster4);
            role.Attack(monster4);

            Console.ReadLine();
        }
    }
}
```

程序运行结果如下：

<!-- ![02_3.gif](https://i.loli.net/2017/09/15/59bbae64f08ba.gif) -->
{% asset_img 2.webp 结果023 %}

**图1.2 小李程序的运行结果**

### 架构师的建议

小李阐述完自己的想法并演示了 Demo 后，项目组长 Peter 首先肯定了小李的思考能力、编程能力以及初步的面向对象分析与设计的思想，并承认小李的程序正确完成了需求中的功能。但同时，Peter 也指出小李的设计存在一些问题，他请小于讲一下自己的看法。 小于是一名有五年软件架构经验的架构师，对软件架构、设计模式和面向对象思想有较深入的认识。他向 Peter 点了点头，发表了自己的看法： “小李的思考能力是不错的，有着基本的面向对象分析设计能力，并且程序正确完成了所需要的功能。不过，这里我想从架构角度，简要说一下我认为这个设计中存在的问题。 首先，小李设计的 `Role` 类的 `Attack` 方法很长，并且方法中有一个冗长的 `if…else` 结构，且每个分支的代码的业务逻辑很相似，只是很少的地方不同。 再者，我认为这个设计比较大的一个问题是，违反了 OCP 原则。在这个设计中，如果以后我们增加一个新的武器，如倚天剑，每次攻击损失 500HP，那么，我们就要打开 `Role`，修改 `Attack` 方法。而我们的代码应该是对修改关闭的，当有新武器加入的时候，应该使用扩展完成，避免修改已有代码。 一般来说，当一个方法里面出现冗长的 `if…else` 或 `switch…case` 结构，且每个分支代码业务相似时，往往预示这里应该引入多态性来解决问题。而这里，如果把不同武器攻击看成一个策略，那么引入策略模式（ _Strategy Pattern_ ）是明智的选择。 最后说一个小的问题，被攻击后，减HP、死亡判断等都是怪物的职责，这里放在 `Role` 中有些不当。”

> Tip：OCP 原则，即开放关闭原则，指设计应该对扩展开放，对修改关闭。 Tip：策略模式，英文名 _Strategy Pattern_ ，指定义算法族，分别封装起来，让他们之间可以相互替换，此模式使得算法的变化独立于客户。

小于边说，边画了一幅 UML 类图，用于直观表示他的思想：、

<!-- ![03_3.jpg](https://i.loli.net/2017/09/15/59bbaf77f3db6.jpg) -->
{% asset_img 3.webp uml类图 %}

**图1.3 小于的设计**

Peter 让小李按照小于的设计重构 Demo，小李看了看小于的设计图，很快完成。相关代码如下：

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace IGameLiAdv
{
    internal interface IAttackStrategy
    {
        void AttackTarget(Monster monster);
    }
}
```

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace IGameLiAdv
{
    internal sealed class WoodSword : IAttackStrategy
    {
        public void AttackTarget(Monster monster)
        {
            monster.Notify(20);
        }
    }
}
```

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace IGameLiAdv
{
    internal sealed class IronSword : IAttackStrategy
    {
        public void AttackTarget(Monster monster)
        {
            monster.Notify(50);
        }
    }
}
```

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace IGameLiAdv
{
    internal sealed class MagicSword : IAttackStrategy
    {
        private Random _random = new Random();

        public void AttackTarget(Monster monster)
        {
            Int32 loss = (_random.NextDouble() < 0.5) ? 100 : 200;
            if (200 == loss)
            {
                Console.WriteLine("出现暴击！！！");
            }
            monster.Notify(loss);
        }
    }
}
```

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace IGameLiAdv
{
    /// <summary>
    /// 怪物
    /// </summary>
    internal sealed class Monster
    {
        /// <summary>
        /// 怪物的名字
        /// </summary>
        public String Name { get; set; }

        /// <summary>
        /// 怪物的生命值
        /// </summary>
        private Int32 HP { get; set; }

        public Monster(String name,Int32 hp)
        {
            this.Name = name;
            this.HP = hp;
        }

        /// <summary>
        /// 怪物被攻击时，被调用的方法，用来处理被攻击后的状态更改
        /// </summary>
        /// <param name="loss">此次攻击损失的HP</param>
        public void Notify(Int32 loss)
        {
            if (this.HP <= 0)
            {
                Console.WriteLine("此怪物已死");
                return;
            }

            this.HP -= loss;
            if (this.HP <= 0)
            {
                Console.WriteLine("怪物" + this.Name + "被打死");
            }
            else
            {
                Console.WriteLine("怪物" + this.Name + "损失" + loss + "HP");
            }
        }
    }
}
```

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace IGameLiAdv
{
    /// <summary>
    /// 角色
    /// </summary>
    internal sealed class Role
    {
        /// <summary>
        /// 表示角色目前所持武器
        /// </summary>
        public IAttackStrategy Weapon { get; set; }

        /// <summary>
        /// 攻击怪物
        /// </summary>
        /// <param name="monster">被攻击的怪物</param>
        public void Attack(Monster monster)
        {
            this.Weapon.AttackTarget(monster);
        }
    }
}
```

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace IGameLiAdv
{
    class Program
    {
        static void Main(string[] args)
        {
            //生成怪物
            Monster monster1 = new Monster("小怪A", 50);
            Monster monster2 = new Monster("小怪B", 50);
            Monster monster3 = new Monster("关主", 200);
            Monster monster4 = new Monster("最终Boss", 1000);

            //生成角色
            Role role = new Role();

            //木剑攻击
            role.Weapon = new WoodSword();
            role.Attack(monster1);

            //铁剑攻击
            role.Weapon = new IronSword();
            role.Attack(monster2);
            role.Attack(monster3);

            //魔剑攻击
            role.Weapon = new MagicSword();
            role.Attack(monster3);
            role.Attack(monster4);
            role.Attack(monster4);
            role.Attack(monster4);
            role.Attack(monster4);
            role.Attack(monster4);

            Console.ReadLine();
        }
    }
}
```

编译运行以上代码，得到的运行结果与上一版本代码基本一致。

### 小李的小结

Peter 显然对改进后的代码比较满意，他让小李对照两份设计和代码，进行一个小结。小李简略思考了一下，并结合小于对一次设计指出的不足，说道： “我认为，改进后的代码有如下优点： 第一，虽然类的数量增加了，但是每个类中方法的代码都非常短，没有了以前 `Attack` 方法那种很长的方法，也没有了冗长的 `if…else`，代码结构变得很清晰。 第二，类的职责更明确了。在第一个设计中，`Role` 不但负责攻击，还负责给怪物减少 `HP` 和判断怪物是否已死。这明显不应该是 `Role` 的职责，改进后的代码将这两个职责移入Monster内，使得职责明确，提高了类的内聚性。 第三，引入 Strategy 模式后，不但消除了重复性代码，更重要的是，使得设计符合了 OCP。如果以后要加一个新武器，只要新建一个类，实现 `IAttackStrategy` 接口，当角色需要装备这个新武器时，客户代码只要实例化一个新武器类，并赋给 `Role` 的 `Weapon` 成员就可以了，已有的 `Role` 和 `Monster` 代码都不用改动。这样就实现了对扩展开发，对修改关闭。” Peter和小于听后都很满意，认为小李总结的非常出色。 IGame公司的讨论会还在进行着，内容是非常精彩，不过我们先听到这里，因为，接下来，我们要对其中某些问题进行一点探讨。别忘了，本文的主题可是依赖注入，这个主角还没登场呢！让主角等太久可不好。

##  探究依赖注入

### 故事的启迪

我们现在静下心来，再回味一下刚才的故事。因为，这个故事里面隐藏着**依赖注入**的出现原因。我说过不只一次，想真正认清一个事物，不能只看“它是什么？什么样子？”，而应该先弄清楚“它是怎么来的？是什么样的需求和背景促使了它的诞生？它被创造出来是做什么用的？”。 回想上面的故事。刚开始，主要需求是一个打怪的功能。小李做了一个初步面向对象的设计：抽取领域场景中的实体（怪物、角色等），封装成类，并为各个类赋予属性与方法，最后通过类的交互完成打怪功能，这应该算是面向对象设计的初级阶段。 在小李的设计基础上，架构师小于指出了几点不足，如不符合 OCP，职责划分不明确等等，并根据情况引入策略模式。这是更高层次的面向对象设计。其实就核心来说，小于只做了一件事：利用多态性，隔离变化。它清楚认识到，这个打怪功能中，有些业务逻辑是不变的，如角色攻击怪物，怪物减少 HP，减到 0 怪物就会死；而变化的仅仅是不同的角色持有不同武器时，每次攻击的效用不一样。于是他的架构，本质就是把变化的部分和不变的部分隔离开，使得变化部分发生变化时，不变部分不受影响。 我们再仔细看看小于的设计图，这样设计后，有个基本的问题需要解决：现在 `Role` 不依赖具体武器，而仅仅依赖一个 `IAttackStrategy` 接口，接口是不能实例化的，虽然 `Role` 的 `Weapon` 成员类型定义为 `IAttackStrategy`，但最终还是会被赋予一个实现了 `IAttackStrategy` 接口的具体武器，并且随着程序进展，一个角色会装备不同的武器，从而产生不同的效用。赋予武器的职责，在 Demo 中是放在了测试代码里。 这里，测试代码实例化一个具体的武器，并赋给 `Role` 的 `Weapon` 成员的过程，就是**依赖注入**！这里要清楚，**依赖注入**其实是一个过程的称谓！

### 正式定义依赖注入

下面，用稍微正式一点的语言，定义依赖注入产生的背景缘由和依赖注入的含义。在读的过程中，读者可以结合上面的例子进行理解。 依赖注入产生的背景： 随着面向对象分析与设计的发展，一个良好的设计，核心原则之一就是将变化隔离，使得变化部分发生变化时，不变部分不受影响（这也是 OCP 的目的）。为了做到这一点，要利用面向对象中的多态性，使用多态性后，客户类不再直接依赖服务类，而是依赖于一个抽象的接口，这样，客户类就不能在内部直接实例化具体的服务类。但是，客户类在运作中又客观需要具体的服务类提供服务，因为接口是不能实例化去提供服务的。就产生了“客户类不准实例化具体服务类”和“客户类需要具体服务类”这样一对矛盾。为了解决这个矛盾，开发人员提出了一种模式：客户类（如上例中的 `Role`）定义一个注入点（`Public` 成员 `Weapon`），用于服务类（实现 `IAttackStrategy` 的具体类，如 `WoodSword`、`IronSword` 和 `MagicSword`，也包括以后加进来的所有实现 `IAttackStrategy` 的新类）的注入，而客户类的客户类（`Program`，即测试代码）负责根据情况，实例化服务类，注入到客户类中，从而解决了这个矛盾。 依赖注入的正式定义： 依赖注入（ _Dependency Injection_ ），是这样一个过程：由于某客户类只依赖于服务类的一个接口，而不依赖于具体服务类，所以客户类只定义一个注入点。在程序运行过程中，客户类不直接实例化具体服务类实例，而是客户类的运行上下文环境或专门组件负责实例化服务类，然后将其注入到客户类中，保证客户类的正常运行。


## 依赖注入那些事儿

上面我们从需求背景的角度，讲述了依赖注入的来源和定义。但是，如果依赖注入仅仅就只有这么点东西，那也没有什么值得讨论的了。但是，上面讨论的仅仅是依赖注入的内涵，其外延还是非常广泛的，从依赖注入衍生出了很多相关的概念与技术，下面我们讨论一下依赖注入的“那些事儿”。

### 依赖注入的类别

依赖注入有很多种方法，上面看到的例子中，只是其中的一种，下面分别讨论不同的依赖注入类型。

####    Setter 注入

第一种依赖注入的方式，就是 Setter 注入，上面的例子中，将武器注入 `Role` 就是 Setter 注入。正式点说： Setter 注入（ _Setter Injection_ ）是指在客户类中，设置一个服务类接口类型的数据成员，并设置一个 `Set` 方法作为注入点，这个 `Set` 方法接受一个具体的服务类实例为参数，并将它赋给服务类接口类型的数据成员。

<!-- ![04_6.jpg](https://i.loli.net/2017/09/15/59bbb21e689a0.jpg) -->
{% asset_img 4.webp img046 %}

**图3.1 Setter注入示意**

上图展示了Setter注入的结构示意图，客户类 `ClientClass` 设置 `IServiceClass` 类型成员 `_serviceImpl`，并设置 `Set_ServiceImpl` 方法作为注入点。`Context` 会负责实例化一个具体的 `ServiceClass`，然后注入到 `ClientClass` 里。 下面给出 Setter 注入的示例代码：

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace SetterInjection
{
    internal interface IServiceClass
    {
        String ServiceInfo();
    }
}
```

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace SetterInjection
{
    internal class ServiceClassA : IServiceClass
    {
        public String ServiceInfo()
        {
            return "我是ServceClassA";
        }
    }
}
```

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace SetterInjection
{
    internal class ServiceClassB : IServiceClass
    {
        public String ServiceInfo()
        {
            return "我是ServceClassB";
        }
    }
}
```

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace SetterInjection
{
    internal class ClientClass
    {
        private IServiceClass _serviceImpl;

        public void Set_ServiceImpl(IServiceClass serviceImpl)
        {
            this._serviceImpl = serviceImpl;
        }

        public void ShowInfo()
        {
            Console.WriteLine(_serviceImpl.ServiceInfo());
        }
    }
}
```

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace SetterInjection
{
    class Program
    {
        static void Main(string[] args)
        {
            IServiceClass serviceA = new ServiceClassA();
            IServiceClass serviceB = new ServiceClassB();
            ClientClass client = new ClientClass();

            client.Set_ServiceImpl(serviceA);
            client.ShowInfo();
            client.Set_ServiceImpl(serviceB);
            client.ShowInfo();
        }
    }
}
```

运行结果如下：

<!-- ![05_3.jpg](https://i.loli.net/2017/09/15/59bbb2c0e0be7.jpg) -->
{% asset_img 5.jpg 结果053 %}

**图3.2 Setter注入运行结果**

####    构造注入

另外一种依赖注入方式，是通过客户类的构造函数，向客户类注入服务类实例。 构造注入（ _Constructor Injection_ ）是指在客户类中，设置一个服务类接口类型的数据成员，并以构造函数为注入点，这个构造函数接受一个具体的服务类实例为参数，并将它赋给服务类接口类型的数据成员。

<!-- ![06_3.jpg](https://i.loli.net/2017/09/15/59bbb3267fef0.jpg) -->
{% asset_img 6.webp img063 %}

**图3.3 构造注入示意**

图3.3是构造注入的示意图，可以看出，与 Setter 注入很类似，只是注入点由 Setter 方法变成了构造方法。这里要注意，由于构造注入只能在实例化客户类时注入一次，所以一点注入，程序运行期间是没法改变一个客户类对象内的服务类实例的。 由于构造注入和 Setter 注入的 `IServiceClass`，`ServiceClassA` 和 `ServiceClassB` 是一样的，所以这里给出另外 `ClientClass` 类的示例代码：

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace ConstructorInjection
{
    internal class ClientClass
    {
        private IServiceClass _serviceImpl;

        public ClientClass(IServiceClass serviceImpl)
        {
            this._serviceImpl = serviceImpl;
        }

        public void ShowInfo()
        {
            Console.WriteLine(_serviceImpl.ServiceInfo());
        }
    }
}
```

可以看到，唯一的变化就是构造函数取代了 `Set_ServiceImpl` 方法，成为了注入点。

####    依赖获取

上面提到的注入方式，都是客户类被动接受所依赖的服务类，这也符合“注入”这个词。不过还有一种方法，可以和依赖注入达到相同的目的，就是依赖获取。 依赖获取（ _Dependency Locate_ ）是指在系统中提供一个获取点，客户类仍然依赖服务类的接口。当客户类需要服务类时，从获取点主动取得指定的服务类，具体的服务类类型由获取点的配置决定。 可以看到，这种方法变被动为主动，使得客户类在需要时主动获取服务类，而将多态性的实现封装到获取点里面。获取点可以有很多种实现，也许最容易想到的就是建立一个Simple Factory作为获取点，客户类传入一个指定字符串，以获取相应服务类实例。如果所依赖的服务类是一系列类，那么依赖获取一般利用 Abstract Factory 模式构建获取点，然后，将服务类多态性转移到工厂的多态性上，而工厂的类型依赖一个外部配置，如XML文件。 不过，不论使用 Simple Factory 还是 Abstract Factory，都避免不了判断服务类类型或工厂类型，这样系统中总要有一个地方存在不符合 OCP 的 `if…else` 或 `switch…case` 结构，这种缺陷是 Simple Factory 和 Abstract Factory 以及依赖获取本身无法消除的，而在某些支持反射的语言中（如 C#），通过将反射机制的引入彻底解决了这个问题（后面讨论）。 下面给一个具体的例子，现在我们假设有个程序，既可以使用 Windows 风格外观，又可以使用 Mac 风格外观，而内部业务是一样的。

<!-- ![07_3.jpg](https://i.loli.net/2017/09/15/59bbb3d9a6b9f.jpg) -->
{% asset_img 7.webp img073 %}

**图3.4 依赖获取示意**

上图乍看有点复杂，不过如果读者熟悉 Abstract Factory 模式，应该能很容易看懂，这就是 Abstract Factory 在实际中的一个应用。这里的 Factory Container 作为获取点，是一个静态类，它的“**Type 构造函数**”依据外部的XML配置文件，决定实例化哪个工厂。下面还是来看示例代码。由于不同组件的代码是相似的，这里只给出 Button 组件的示例代码，完整代码请参考文末附上的完整源程序。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace DependencyLocate
{
    internal interface IButton
    {
        String ShowInfo();
    }
}
```

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace DependencyLocate
{
    internal sealed class WindowsButton : IButton
    {
        public String Description { get; private set; }

        public WindowsButton()
        {
            this.Description = "Windows风格按钮";
        }

        public String ShowInfo()
        {
            return this.Description;
        }
    }
}
```

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace DependencyLocate
{
    internal sealed class MacButton : IButton
    {
        public String Description { get; private set; }

        public MacButton()
        {
            this.Description = " Mac风格按钮";
        }

        public String ShowInfo()
        {
            return this.Description;
        }
    }
}
```

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace DependencyLocate
{
    internal interface IFactory
    {
        IWindow MakeWindow();

        IButton MakeButton();

        ITextBox MakeTextBox();
    }
}
```

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace DependencyLocate
{
    internal sealed class WindowsFactory : IFactory
    {
        public IWindow MakeWindow()
        {
            return new WindowsWindow();
        }

        public IButton MakeButton()
        {
            return new WindowsButton();
        }

        public ITextBox MakeTextBox()
        {
            return new WindowsTextBox();
        }
    }
}
```

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace DependencyLocate
{
    internal sealed class MacFactory : IFactory
    {
        public IWindow MakeWindow()
        {
            return new MacWindow();
        }

        public IButton MakeButton()
        {
            return new MacButton();
        }

        public ITextBox MakeTextBox()
        {
            return new MacTextBox();
        }
    }
}
```

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Xml;

namespace DependencyLocate
{
    internal static class FactoryContainer
    {
        public static IFactory factory { get; private set; }

        static FactoryContainer()
        {
            XmlDocument xmlDoc = new XmlDocument();
            xmlDoc.Load("http://www.cnblogs.com/Config.xml");
            XmlNode xmlNode = xmlDoc.ChildNodes[1].ChildNodes[0].ChildNodes[0];

            if ("Windows" == xmlNode.Value)
            {
                factory = new WindowsFactory();
            }
            else if ("Mac" == xmlNode.Value)
            {
                factory = new MacFactory();
            }
            else
            {
                throw new Exception("Factory Init Error");
            }
        }
    }
}
```

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace DependencyLocate
{
    class Program
    {
        static void Main(string[] args)
        {
            IFactory factory = FactoryContainer.factory;
            IWindow window = factory.MakeWindow();
            Console.WriteLine("创建 " + window.ShowInfo());
            IButton button = factory.MakeButton();
            Console.WriteLine("创建 " + button.ShowInfo());
            ITextBox textBox = factory.MakeTextBox();
            Console.WriteLine("创建 " + textBox.ShowInfo());

            Console.ReadLine();
        }
    }
}
```

这里我们用XML作为配置文件。配置文件 `Config.xml` 如下：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<config>
    <factory>Mac</factory>
</config>
```

可以看到，这里我们将配置设置为 Mac 风格，编译运行上述代码，运行结果如下：

<!-- ![08_3.jpg](https://i.loli.net/2017/09/15/59bbb4ede424d.jpg) -->
{% asset_img 8.jpg 结果083 %}

**图3.5 配置Mac风格后的运行结果**

现在，我们不动程序，仅仅将配置文件中的“Mac”改为 Windows，运行后结果如下：

<!-- ![09_3.jpg](https://i.loli.net/2017/09/15/59bbb4d5af22b.jpg) -->
{% asset_img 9.jpg 结果093 %}

**图3.6 配置为Windows风格后的运行结果**

从运行结果看出，我们仅仅通过修改配置文件，就改变了整个程序的行为（我们甚至没有重新编译程序），这就是多态性的威力，也是依赖注入效果。 本节共讨论了三种基本的依赖注入类别，有关更多依赖注入类别和不同类别对比的知识，可以参考 Martin Fowler 的《[_Inversion of Control Containers and the Dependency Injection pattern_](https://www.martinfowler.com/articles/injection.html)》。

### 反射与依赖注入

回想上面 _Dependency Locate_ 的例子，我们虽然使用了多态性和 _Abstract Factory_ ，但对 OCP 贯彻的不够彻底。在理解这点前，朋友们一定要注意潜在扩展在哪里，潜在会出现扩展的地方是“新的组件系列”而不是“组件种类”，也就是说，这里我们假设组件就三种，不会增加新的组件，但可能出现新的外观系列，如需要加一套 _Ubuntu_ 风格的组件，我们可以新增 _UbuntuWindow_ 、 _UbuntuButton_ 、 _UbuntuTextBox_ 和 _UbuntuFactory_ ，并分别实现相应接口，这是符合 OCP 的，因为这是扩展。但我们除了修改配置文件，还要无可避免的修改 _FactoryContainer_ ，需要加一个分支条件，这个地方破坏了 OCP。依赖注入本身是没有能力解决这个问题的，但如果语言支持反射机制（ _Reflection_ ），则这个问题就迎刃而解。 我们想想，现在的难点是出在这里：对象最终还是要通过“new”来实例化，而“`new`”只能实例化当前已有的类，如果未来有新类添加进来，必须修改代码。如果，我们能有一种方法，不是通过“`new`”，而是通过类的名字来实例化对象，那么我们只要将类的名字作为配置项，就可以实现在不修改代码的情况下，加载未来才出现的类。所以，反射给了语言“预见未来”的能力，使得多态性和依赖注入的威力大增。 下面是引入反射机制后，对上面例子的改进：

<!-- ![10_3.jpg](https://i.loli.net/2017/09/15/59bbb60a2b744.jpg) -->
{% asset_img 10.webp img103 %}

**图3.7 引入反射机制的Dependency Locate**

可以看出，引入反射机制后，结构简单了很多，一个反射工厂代替了以前的一堆工厂，`Factory Container` 也不需要了。而且以后有新组件系列加入时，反射工厂是不用改变的，只需改变配置文件就可以完成。下面给出反射工厂和配置文件的代码。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Reflection;
using System.Xml;

namespace DependencyLocate
{
    internal static class ReflectionFactory
    {
        private static String _windowType;
        private static String _buttonType;
        private static String _textBoxType;

        static ReflectionFactory()
        {
            XmlDocument xmlDoc = new XmlDocument();
            xmlDoc.Load("http://www.cnblogs.com/Config.xml");
            XmlNode xmlNode = xmlDoc.ChildNodes[1].ChildNodes[0];

            _windowType = xmlNode.ChildNodes[0].Value;
            _buttonType = xmlNode.ChildNodes[1].Value;
            _textBoxType = xmlNode.ChildNodes[2].Value;
        }

        public static IWindow MakeWindow()
        {
            return Assembly.Load("DependencyLocate").CreateInstance("DependencyLocate." + _windowType) as IWindow;
        }

        public static IButton MakeButton()
        {
            return Assembly.Load("DependencyLocate").CreateInstance("DependencyLocate." + _buttonType) as IButton;
        }

        public static ITextBox MakeTextBox()
        {
            return Assembly.Load("DependencyLocate").CreateInstance("DependencyLocate." + _textBoxType) as ITextBox;
        }
    }
}
```

配置文件如下：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<config>
    <window>MacWindow</window>
    <button>MacButton</button>
    <textBox>MacTextBox</textBox>
</config>
```

反射不仅可以与 _Dependency Locate_ 结合，也可以与 _Setter Injection_ 与 _Construtor Injection_ 结合。反射机制的引入，降低了依赖注入结构的复杂度，使得依赖注入彻底符合 OCP，并为通用依赖注入框架（如 Spring.NET 中的 IoC 部分、Unity 等）的设计提供了可能性。

### 多态的活性与依赖注入

####    多态性的活性

这一节我们讨论多态的活性及其与依赖注入类型选择间密切的关系。 首先说明，“多态的活性”这个术语是我个人定义的，因为我没有找到既有的概念名词可以表达我的意思，所以就自己造了一个词。这里，某多态的活性是指被此多态隔离的变化所发生变化的频繁程度，频繁程度越高，则活性越强，反之亦然。 上文说过，多态性可以隔离变化，但是，不同的变化，发生的频率是不一样的，这就使得多态的活性有所差别，这种差别影响了依赖注入的类型选择。 举例来说，本文最开始提到的武器多态性，其活性非常高，因为在那个程序中，_Role_ 在一次运行中可能更换多次武器。而现在我们假设 _Role_ 也实现了多态性，这是很可能的，因为在游戏中，不同类型的角色（如暗夜精 灵、牛头人、矮人等）很多属性和业务是想通的，所以很可能通过一个 _IRole_ 或 _AbstractRole_ 抽象类实现多态性，不过，_Role_ 在实例化后（一般在用户登录成功后），是不会变化的，很少有游戏允许同一个玩家在运行中变换 _Role_ 类型，所以 _Role_ 应该是一但实例化，就不会变化，但如果再实例化一个（如另一个玩家登录），则可能就变化了。最后，还有一种多态性是活性非常低的，如我们熟悉的数据访问层多态性，即使我们实现了 SQL Server、Oracle 和 Access 等多种数据库的访问层，并实现了依赖注入，但几乎遇不到程序运行着就改数据库或短期内数据库频繁变动的情况。 以上不同的多态性，不但特征不同，其目的一般也不同，总结如下： 高活多态性——指在客户类实例运行期间，服务类可能会改变的多态性。 中活多态性——指在客户类实例化后，服务类不会改变，但同一时间内存在的不同实例可能拥有不同类型的服务类。 低活多态性——指在客户类实例化后，服务类不会改变，且同一时间内所有客户类都拥有相同类型的服务类。 以上三种多态性，比较好的例子就是上文提到的武器多态性（高活）、角色多态性（中活）和数据访问层多态性（低活）。另外，我们说一种多态性是空间稳定的，如果同一客户类在同一时间内的所有实例都依赖相同类型的服务类，反之则叫做空间不稳定多态性。我们说一种多态性是时间稳定的，如果一个客户类在实例化后，所以来的服务类不能再次更改，反之则叫做时间不稳定多态性。显然，高活多态性时间和空间均不稳定；中活多态性是时间稳定的，但空间不稳定；低活多态性时间空间均稳定。

####    不同活性多态的依赖注入选择

一般来说，高活多态性适合使用 Setter 注入。因为 Setter 注入最灵活，也是唯一允许在同一客户类实例运行期间更改服务类的注入方式。并且这种注入一般由上下文环境通过 Setter 的参数指定服务类类型，方便灵活，适合频繁变化的高活多态性。 对于中活多态性，则适合使用 Constructor 注入。因为 Constructor 注入也是由上下文环境通过 Construtor 的参数指定服务类类型，但一点客户类实例化后，就不能进行再次注入，保证了其时间稳定性。 而对于低活多态性，则适合使用 _Dependency Locate_ 并配合文件配置进行依赖注入，或 Setter、Constructor 配合配置文件注入，因为依赖源来自文件，如果要更改服务类，则需要更改配置文件，一则确保了低活多态性的时间和空间稳定性，二是更改配置文件的方式方便于大规模服务类替换。（因为低活多态性一旦改变行为，往往规模很大，如替换整个数据访问层，如果使用 Setter 和 Construtor 传参，程序中需要改变的地方不计其数） 本质上，这种选择是因为不同的依赖注入类型有着不同的稳定性，大家可以细细体会“活性”、“稳定性”和“依赖注入类型”之间密切的关系。


## IoC Container

### IoC Container 出现的必然性

上面讨论了诸多依赖注入的话题。说道依赖注入，就不能不说 _IoC Container_ （IoC 容器），那么到底什么是 _IoC_ 容器？我们还是先来看看它的出现背景。 我们知道，软件开发领域有句著名的论断：不要重复发明轮子！因为软件开发讲求复用，所以，对于应用频繁的需求，总是有人设计各种通用框架和类库以减轻人们的开发负担。例如，数据持久化是非常频繁的需求，于是各种 _ORM_ 框架应运而生；再如，对 _MVC_ 的需求催生了 _Struts_ 等一批用来实现 _MVC_ 的框架。 随着面向对象分析与设计的发展和成熟，_OOA&D_ 被越来越广泛应用于各种项目中，然而，我们知道，用 _OO_ 就不可能不用多态性，用多态性就不可能不用依赖注入，所以，依赖注入变成了非常频繁的需求，而如果全部手工完成，不但负担太重，而且还容易出错。再加上反射机制的发明，于是，自然有人开始设计开发各种用于依赖注入的专用框架。这些专门用于实现依赖注入功能的组件或框架，就是 _IoC Container_ 。 从这点看，_IoC Container_ 的出现有其历史必然性。目前，最著名的 _IoC_ 也许就是 _Java_ 平台上的 _Spring_ 框架的 _IoC_ 组件，而 _.NET_ 平台上也有 _Spring.NET_ 和 _Unity_ 等。

### IoC Container 的分类

前面曾经讨论了三种依赖注入方式，但是，想通过方式对 _IoC Container_ 进行分类很困难，因为现在 _IoC Container_ 都设计很完善，几乎支持所有依赖注入方式。不过，根据不同框架的特性和惯用法，还是可以讲 _IoC Container_ 分为两个大类。

####    重量级 IoC Container

所谓重量级 _IoC Container_ ，是指一般用外部配置文件（一般是 _XML_ ）作为依赖源，并托管整个系统各个类的实例化的 _IoC Container_ 。这种 _IoC Container_ ，一般是承接了整个系统几乎所有多态性的依赖注入工作，并承接了所有服务类的实例化工作，而且这些实例化依赖于一个外部配置文件，这种 _IoC Container_ ，很像通过一个文件，定义整个系统多态结构，视野宏大，想要很好驾驭这种 _IoC Container_ ，需要一定的架构设计能力和丰富的实践经验。 _Spring_ 和 _Spring.NET_ 是重量级 _IoC Container_ 的例子。一般来说，这种 _IoC Container_ 稳定性有余而活性不足，适合进行低活多态性的依赖注入。

####    轻量级 IoC Container

还有一种 _IoC Container_ ，一般不依赖外部配置文件，而主要使用传参的 Setter 或 Construtor 注入，这种IoC Container叫做轻量级 _IoC Container_ 。这种框架很灵活，使用方便，但往往不稳定，而且依赖点都是程序中的字符串参数，所以，不适合需要大规模替换和相对稳定的低活多态性，而对于高活多态性，有很好的效果。 _Unity_ 是一个典型的轻量级 _IoC Container_ 。

### .NET平台上典型 IoC Container 推介

####    Spring.NET

<!-- ![11_3.png](https://i.loli.net/2017/09/15/59bbb86a93913.png) -->
{% asset_img 11.png img113 %}

_Spring.NET_ 是 _Java_ 平台上 _Spring_ 对 _.NET_ 平台的移植，使用方法和 _Spring_ 很像，并且功能强大，是 _.NET_ 平台上大中型开发 _IoC Container_ 的首选之一。除了 _DI_ 外，_Spring.NET_ 也包括 _AOP_ 等诸多功能。 _Spring.NET_ 的官方网站是：http://www.springframework.net/

####    Unity

<!-- ![12_3.gif](https://i.loli.net/2017/09/15/59bbb8f4a6954.gif) -->
{% asset_img 12.webp img123 %}

对于小型项目和讲求敏捷的团队，_Spring.NET_ 可能有点太重量级，那么可以选择轻量级的 _Unity_ 。_Unity_ 是微软 _patterns & practices_ 团队推出的轻量级框架，非常好用，目前最新版本是 1.2。

_Unity_ 的官方网站是：http://unity.codeplex.com/

##  参考文献

1.  [Shivprasad koirala, Design pattern – Inversion of control and Dependency injection](http://www.codeproject.com/KB/aspnet/IOCDI.aspx)

2.  [Martin Fowler, Inversion of Control Containers and the Dependency Injection pattern](http://www.martinfowler.com/articles/injection.html)

3.  [Paul, IoC Types](http://docs.codehaus.org/display/PICO/IoC+Types)

4.  Eric Freeman, Elisabeth Freeman. Head First Design Patterns. O’Reilly Media, 2004. ISBN 0596007142

5.  Erich Gamma, Richard Helm, Ralph Johnson, John Vlissides. Design Patterns: Elements of Reusable Object-Oriented Software. Addison-Wesley, 1995. ISBN 0201633612

6.  Patrick Smacchia 著，施凡等 译，C#和.NET2.0 平台、语言与框架。2008.1，人民邮电出版

7.  Jeffrey Rechter 著，CLR via C#（影印版）。2008.8，人民邮电出版

---
title: 使用枚举和按位运算来控制用户权限
url: 307.html
id: 307
categories:
  - 'C#'
date: 2018-12-07 14:39:46
tags:
---

0.  起因

    公司重构系统，我被分配到了用户管理模块，在做到权限的时候发现之前的权限管理是用一个长的字符串来存储所有权限的，例如：

    <!-- more -->

    ```
    5101110
    ABCDEFG
    ```

    上面的 `7` 位数字分别对应下面的 `7` 个权限，除了第一位为权限等级（分为 1 至 5 档）外，其他的都是是否拥有该权限的状态（1 代表拥有该权限，0 代表没有该权限）。 这样的做法虽然看似简单，但在代码编写中会麻烦很多，特别是当权限种类数量很多的时候。所以，在查阅资料后，我决定**使用枚举和按位运算来控制用户权限**（点题），在向 leader 请示后，开始改造。

1.  原理

    首先要把原有的权限拆分为两部分，一部分是第一位的权限等级，另一部分是各个权限状态，需要改造的主要是各个权限状态。 为了简化步骤，我把权限数量缩小为 `6` 个，实际有 `15` 个（并且未来还可能增加），具体是这样的： 一个六个权限，分别用 0 和 1 来表示**有**或**没有**对应的权限：

    ```
    17

    FEDCBA
    010001B
    ```

    如上，代表用户拥有 `A` 权限和 `E` 权限，同理可得如果有 一个用户拥有 `B` 权限、`C` 权限和 `D` 权限，那我们就可以这样计算：

    ```
    2 | 4 | 8 = 14

      000010B
      000100B
    | 001000B
    ---------
      001110B
    ```

    然后我们就可以用 `001110` 来代表用户所拥有的权限了。 当我们需要**添加权限**的时候，我们就可以用**按位或**（`|`）来计算，比如我们要为上面的 `001110B` 来添加一个 F 权限（`100000B`）：

    ```
    001110B | 100000B = 101110B

    14 | 32 = 45
    ```

    而且就算用户已经有了需要添加的权限，再进行**按位或**的运算也是没有问题的：

    ```
    45 | 32 = 45

    101110B | 100000B = 101110B
    ```

    当我们需要**移除权限**的时候，我们就要用到**与非运算**（`&` 和 `~`）：

    ```
    45 & ~32 = 14

    101110B & ~100000B = 001110B
    ```

    同样的，就算用户本来没有这个权限，在移除权限时也不会有问题：

    ```
    14 & ~32 = 14

    001110B & ~100000B = 001110B
    ```

    当我们需要判断一个用户**是否拥有权限**的时候，可以用用户的权限来**与**（`&`）要判断的权限，当结果还是判断的权限时，则代表用户有这个权限：

    ```
    45 & 32 = 32

    101110B & 100000B = 100000B
    ```

2.  编码

    首先列出所有的权限的枚举：

    ```csharp
    /// <summary>
    /// 权限集枚举
    /// </summary>
    public enum RightsSet
    {
        /// <summary>
        /// A 权限
        /// </summary>
        ARights = 1,

        /// <summary>
        /// B 权限
        /// </summary>
        BRights = 2,

        /// <summary>
        /// C 权限
        /// </summary>
        CRights = 4,

        /// <summary>
        /// D 权限
        /// </summary>
        DRights = 8,

        /// <summary>
        /// E 权限
        /// </summary>
        ERights = 16,

        /// <summary>
        /// F 权限
        /// </summary>
        FRights = 32
    }
    ```

    注意，枚举的值一定要是 `2` 的 N 次幂形式。 然后是三个与权限相关个公共静态方法：

    ```csharp
    /// <summary>
    /// 判断是否拥有一个权限
    /// </summary>
    /// <param name="userRights">用户原有的权限</param>
    /// <param name="newRights">需要判断的权限</param>
    /// <returns>是否拥有一个权限</returns>
    public static Boolean hasRights(RightsSet userRights, RightsSet newRights)
    {
        return (userRights & newRights) == newRights;
    }

    /// <summary>
    /// 添加一个权限
    /// </summary>
    /// <param name="userRights">用户原有的权限</param>
    /// <param name="newRights">需要添加的权限</param>
    /// <returns>用户的新权限</returns>
    public static RightsSet addRights(RightsSet userRights, RightsSet newRights)
    {
        return userRights | newRights;
    }

    /// <summary>
    /// 删除一个权限
    /// </summary>
    /// <param name="userRights">用户原有的权限</param>
    /// <param name="newRights">需要删除的权限</param>
    /// <returns>用户的新权限</returns>
    public static RightsSet deleteRights(RightsSet userRights, RightsSet newRights)
    {
        return userRights & ~newRights;
    }
    ```

3.  最后

    位运算是最基础的计算机运算，各个语言的用法不会有太大的变化，所以不管是什么语言应该都是可以实现的； 在数据库中我们只需要存储 `RightsSet` 的值就可以代表各种权限的组合结果了； 在数据库操作中，如果我们要对某一类的权限进行处理时，也不需要像以前一样进行复杂的字符串处理了； 并且位运算作为基础的计算机运算的速度是非常快的，各种数据库也都支持位运算； 而在代码编写中，我们只需要使用权限的名称（类似于上方代码中的枚举标志符 `ARights`、`BRights`）来进行操作，而不用记忆各个权限对应的数值； 当在前端进行权限更改等操作时，我们也可以直接在前端计算好 `RightsSet` 的值，再传给后台，反过来前台显示也是一样；

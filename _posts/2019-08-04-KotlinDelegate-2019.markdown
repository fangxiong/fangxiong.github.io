---
layout:     post
title:      "Kotlin使用Delegate,封装SharePreferences"
subtitle:   " \"Kotlin委托的用法\""
date:       2019-08-04 12:00:00
author:     "ML"
header-img: "/img/post-bg-2019.jpg"
tags:
    - kotlin
    - delegate
---
# Kotlin使用委托（Delegate）,封装SharePreferences
>具体参考[AndroidConfig](https://github.com/mamenglong/AndroidConfig)
## 委托的实现
> 委托模式是软件设计模式中的一项基本技巧。在委托模式中，有两个对象参与处理同一个请求，接受请求的对象将请求委托给另一个对象来处理。Kotlin 直接支持委托模式，更加优雅，简洁。Kotlin 通过关键字 by 实现委托，by之前为接受对象（委托者），by后为被委托对象。
* 类委托
  >类的委托即一个类中定义的方法实际是调用另一个类的对象的方法来实现的。以下实例中派生类 Derived 继承了接口 Base 所有方法，并且委托一个传入的 Base 类的对象来执行这些方法.
  ```kotlin
    // 创建接口
    interface Base {   
        fun print()
    }

    // 实现此接口的被委托的类
    class BaseImpl(val x: Int) : Base {
        override fun print() { print(x) }
    }

    // 通过关键字 by 建立委托类
    class Derived(b: Base) : Base by b

    fun main(args: Array<String>) {
        val b = BaseImpl(10)
        Derived(b).print() // 输出 10
    }
  ```
  >在 Derived 声明中，by 子句表示，将 b 保存在 Derived 的对象实例内部，而且编译器将会生成继承自 Base 接口的所有方法, 并将调用转发给 b。
  * 属性委托
  >属性委托指的是一个类的某个属性值不是在类中直接进行定义，而是将其托付给一个代理类，从而实现对该类的属性统一管理。
    + 属性委托语法格式：
      >val/var <属性名>: <类型> by <表达式>
      - var/val：属性类型(可变/只读)
      - 属性名：属性名称
      - 类型：属性的数据类型
      - 表达式：委托代理类
    >by 关键字之后的表达式就是委托, 属性的 get() 方法(以及set() 方法)将被委托给这个对象的getValue() 和 setValue() 方法。属性委托不必实现任何接口, 但必须提供 getValue() 函数(对于 var属性,还需要 setValue() 函数)。
    + 被委托的类
    >该类需要包含 getValue() 方法和 setValue() 方法，且参数 thisRef 为进行委托的类的对象，prop 为进行委托的属性的对象。
    ```kotlin
    import kotlin.reflect.KProperty
    // 定义包含属性委托的类
    class Example {
        var p: String by Delegate()
    }
    // 委托的类
    class Delegate {
        operator fun getValue(thisRef: Any?, property: KProperty<*>): String {
            return "$thisRef, 这里委托了 ${property.name} 属性"
        }

        operator fun setValue(thisRef: Any?, property: KProperty<*>, value: String) {
            println("$thisRef 的 ${property.name} 属性赋值为 $value")
        }
    }
    fun main(args: Array<String>) {
        val e = Example()
        println(e.p)     // 访问该属性，调用 getValue() 函数

        e.p = "Runoob"   // 调用 setValue() 函数
        println(e.p)
    }
    ```
    输出结果：
    >Example@666, 这里委托了 p 属性/n
    Example@666 的 p 属性赋值为 Runoob
    Example@666, 这里委托了 p 属性

title: Swift4 访问控制权限
categories: iOS
date: 2017-12-26 15:21:06
tags:
description:
---

`Swift 3` 中，新增了 `fileprivate` 和 `open` 权限，而在`Swift4` 中，对 `fileprivate` 和 `private` 的访问范围做出了调整，本文来梳理下 `Swift` 中所有的访问控制权限的使用范围。

<!--more-->

首先，访问控制限制你在不同源文件和 `module` 之前代码和代码之前的访问。这个特性让你可以隐藏一些代码的实现，和明确一些可以访问和使用的接口。你可以给类，结构体，枚举，属性，方法，构造器，下标，协议等指定访问等级。

<Br/>



###  Modules 和 源文件

`Swift` 的访问控制模型是基于 `module` 和 源文件的。

在 `Swift` 文档中对 `module` 和源文件的解释如下：

一个 `module` 是一个独立的代码建造单元，例如一个 `framework` 或者 `application` 可以构建和包装成一个可以被其他 `module` 通过 `Swift` 的关键字 `import` 的单元。

在 `Xcode` 中的每个编译 `target` （例如一个 `app bundle` 或者 `framework`）都被视作 `Swift` 里的一个单独的 `module`。如果将应用程序代码的各个部分组合成一个独立的框架，也许可以在多个应用程序中封装和重用这些代码，那么当它被导入并在程序中使用时，或者在另一个 `framework` 中使用时，在该框架中定义的所有内容都将是独立 `module` 的一部分。

一个源文件是在一个 `module` 里单独的 `Swift` 源码（或在 一个 `app` 或 `framework` 里的单独的文件） 。虽然常见的是在分开的源文件里定义单独的类型，但一个单独的源文件也能包含多个类型，方法等的定义。

<Br/>

### 访问级别

`Swift` 提供 5 个不同的访问级别，权限最高的是 `open`，依次是 `public`，`internal`，`fileprivate`，最低的是 `private`。默认使用的级别是 `internal`。

#### open

使用 `open` 和 `public` 标记的实体在他们定义的 `module` 中的任意文件中都可以使用，并且在 `import` 了其定义的 `module` 的其他 `module` 的源文件中也能使用。一般在 `framework` 中指定公开的接口里使用 `open` 或者 `public` 级别。

<Br/>

#### open 和 public 的区别如下

- 拥有 `public` 权限或者更低权限的类，只能在其定义的 `module` 中被子类化
- 拥有 `public` 权限或者更低权限的类的成员，只能在其定义的 `module` 中被重写或子类化
- `open` 权限的类可以在其定义和 `import` 的 `module` 中子类化
- `open` 权限的类成员可以在其定义和 `import` 的 `module` 中被重写或子类化

<Br/>

#### internal

`internal` 修饰的实体在其定义 `module` 中的任意源文件中都可以访问，但是在其他 `module` 的任意源文件中都访问不了。一般在定义`app` 或者 `framework` 内部的结构的时候，使用 `internal` 级别。

<Br/>

#### fileprivate

`fileprivate` 限制了只能在其定义的源文件里面使用。使用 `fileprivate` 权限以隐藏其实现细节。当其只在整个源文件中使用的时候，使用 `fileprivate` 修饰。

<Br/>

#### private

`private` 访问权限限制其只能在定义的范围内，和其在同一文件中的 `extension` 中使用。当其只在当个声明中使用的时候，使用 `private` 修饰。



<Br/>

### 访问权限的使用原则

#### 统一的原则

一个实体不能根据另一个级别更低的实体来定义。

<Br/>

#### 单元测试 Targaets 的访问权限

一般来说，只有标注为 `open` 或 `public` 的实体才能在其他 `module` 中访问，但是，在单元测试的 `target` 中，能访问任意模块中拥有 `@testable` 的实体。

<Br/>

#### 元组

元组的访问级别不能像类，结构体那样单独定义。它的访问级别是自动推断出来的，使用元组元素中最低的级别。

方法的访问权限取决于参数和返回值中最低的访问权限。并且当访问权限于上下文默认的不符时，你必须明确的表明访问权限。例如如下的方法将编译不通过，需要指明 `private` 或者 `fileprivate`。

```
func testFunction() -> (SomeInternalClass, SomePrivateClass) {
    
}
```

<Br/>

#### 枚举

枚举的每个值都和他们所属的枚举拥有相同的级别，并且并不能定义单独的访问级别。

如下，一个 `public` 的枚举，其中的 `north`，`south`，`east`，`west` 的访问级别都是 `public`。

```
public enum CompassPoint {
    case north
    case south
    case east
    case west
}
```

枚举值的关联值或真实值的访问级别也必须至少和枚举值一样高。

<Br/>

#### 子类

子类的访问级别不能高于父类，但是子类复写的父类方法的访问级别可以高于父类。

```
public class A {
    fileprivate func someMethod() {}
}
 
internal class B: A {
    override internal func someMethod() {}
}
```

<Br/>

#### 协议

你可以给协议指定访问级别，并且对于协议的每一项来说，访问级别都和协议相同。并且你不能单独给协议中单独的方法等定义不同的访问级别。

​																																																																																																										




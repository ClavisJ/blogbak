title: "【译】Swift2的新特性"
categories: iOS
date: 2016-02-22 14:01:41
tags: swift2
---
> 作者 Greg Heo, [原文链接](http://www.raywenderlich.com/108522/whats-new-in-swift-2)，原文日期：2015/6/12
> 
> 译者：Clavis 校对：Felicity

我们在WWDC上了解到Swift团队的思路并没有和我们一样被互相传送的粗燥绘画所扰乱，他们在Swift2上下了很多功夫。

我们马上就会有大量的关于Swift2的纸质和视频教程了，与此同时，我将突出展示一些最振奋人心的改变，因此你能做好在秋季就转移到Swift2的准备。

<!--more-->

## 错误处理

就如Ray在他的[WWDC 2015 Initial Impressions](http://www.raywenderlich.com/108379/wwdc-2015-initial-impressions)文章上面提到的那样，错误处理将会在Swift2中改版。我们将使用一个用于异常处理并且看起来更加小的新系统来替代NSError对象和双指针。

也许你会比较熟悉这样的代码：

	if drinkWithError(nil) {
	  print("Could not drink beer! :[")
	  return
	}


通常在Cocoa中，你能在这里传入一个`NSError`对象（Swift中的一个`inout`参数）的引用，这个方法将会分配一个变量。然而，问题是你能在这里通过一个nil对象去完全忽视这个错误；或者，你能通过一个NSError但是从来没有检测过他。

Swift2对错误检测添加了一些额外的安全性，使用throws关键字去
明确那些可以抛出异常的方法和函数。之后你就有do，try，和catch关键字在异常抛出的时候调用一些方法:

	// 1
	enum DrinkError: ErrorType {
	  case NoBeerRemainingError
	}
	 
	// 2
	func drinkWithError() throws {
	  if beer.isAvailable() {
	    // party!
	  } else {
	    // 3
	    throw DrinkError.NoBeerRemainingError
	  }
	}
	 
	func tryToDrink() {
	  // 4
	  do {
	    try drinkWithError()
	  } catch {
	    print("Could not drink beer! :[")
	    return
	  }
	}

这里有些需要强调的事情：
1.去创建一个抛出的错误，可以简单的创建一个ErrorType的派生枚举。
2.你需要去使用throws关键字去标记任意可以抛出异常的方法。
3.抛出的异常将会在section4获取。
4.现在的异常处理替代了try代码块，从其他语言的开发者也许对现在的异常处理更加熟悉，在抛出异常的do block里面，你能包含任何想要的代码。之后给每个方法调用添加key关键字就能抛出异常了。

这个新的语法是非常轻量级的并且可读性好的。任意目前使用了NSError的 API现在都可以使用这个系统，因此我们能看到更多使用这个新语法的。

![](http://cdn1.raywenderlich.com/wp-content/uploads/2015/06/throw-all-the-things.jpg)

## 绑定

使用Swift1.2，我们丢失了“厄运金字塔”和获得在一行中测试绑定多个可选值的能力:

	if let pants = pants, frog = frog {
	  // good stuff here!
	}


这句代码能正常运行，但问题是对于一些人来说更优美的代码路径是所有的可选值都有一些缩进。这就意味着当错误条件排除在外的时候，你需要去看主线部分内部的代码块缩进。

我们希望有什么方法可以检查那些可选值没有值的情况，刚好现在就有这样的方法！那就是Swift2中提供的guard语句：

	guard let pants = pants, frog = frog else {
	  // sorry, no frog pants here :[
	  return
	} 
	// at this point, frog and pants are both unwrapped and bound!


使用guard意味着你能执行值绑定并且在条件不满足的时候在else中提供一个要运行的代码块。接着，你能继续在这个分支下继续并且frog和pants的值将有值并且不再越界。

有了guard语法之后，你能更加明确你真正期望的状态，而不是去检查错误的分支，我们的代码也应该更加清楚。

Note:如果你还是对为什么guard语句比if-else语句更加好用而感到困惑，查看这篇Swift团队成员[Eric Cerney](http://www.raywenderlich.com/u/ecerney)的文章[Swift guard statement](http://ericcerney.com/swift-guard-statement/)吧。

## 协议扩展
面向对象？函数式？还有一个要加在之前的就是面向协议编程的Swift语言！

在Swift1中，协议更像是一个给类，结构体或者枚举明确要遵循的一系列属性和方法的接口。

但是在Swift2中，你能扩展协议，并且给属性方法添加上默认的实现。你现在已经能给类和结构体添加新的属性和方法了，比如给String和Array添加新的方法，但是使用协议来添加这些将会更轻松一些。

	extension CustomStringConvertible {
	  var shoutyDescription: String {
	    return "\(self.description.uppercaseString)!!!"
	  }
	}
	 
	let greetings = ["Hello", "Hi", "Yo yo yo"]
	 
	// prints ["Hello", "Hi", "Yo yo yo"]
	print("\(greetings.description)")
	 
	// prints [HELLO, HI, YO YO YO]!!!
	print("\(greetings.shoutyDescription)")

注意可打印的协议现在可以在CustomStringConvertible中调用，并且能在大多数Foundation对象中使用。有了协议扩展，你能更好的对大片系统或者你自定义的类进行扩展。你能编写一个统一的实现应用在一系列的类型之中，而不是在许多的类、结构体、枚举中添加很多的自定义代码。

Swift团队已经在忙着做这个——即使你没有在Swift中使用过map或者filter，或许你听过他们将会把map和filter做成一个方法而不是全局函数。感谢协议扩展的力量，现在我们在集合类型中能够使用一系列例如map，filter，indexOf等更多的新的方法！

	let numbers = [1, 5, 6, 10, 16, 42, 45]
	 
	// Swift 1
	find(filter(map(numbers, { $0 * 2}), { $0 % 3 == 0 }), 90)
	 
	// Swift 2
	numbers.map { $0 * 2 }.filter { $0 % 3 == 0 }.indexOf(90) // returns 2

感谢协议的一致性，你的Swift2代码能够变得更加简洁和可读性更强。在Swift1版本的时候，你需要到内部去查看理解发生了什么；在Swift版本2时，函数链将会更加清楚。

我们可以利用面向协议编程的能量——查看[WWDC session on this topic](https://developer.apple.com/videos/wwdc/2015/?id=408)并且随时留意之后这个页面的教程和文章！

# 彩蛋

现在已经有很多关于所有sessions的声明了，因此我只想快点强调一些东西：

Objective-C generics-苹果已经开始给所有的Objective-C代码添加注释了，这样在Swift中键入的时候就能得到正确的可选性。配合Objective-C generics将持续给Swift开发者更好的键入提示。如果你希望得到一系列的UITouch对象或者一个数组或是字符串，那么正好现在你能得到的就是这些，而不是一个AnyObjects的集合。

重命名语法 - println仅仅只用了一年，现在我们又用回了原始的print，现在的print有一个默认的第二个参数，通过设置true来标明是打印新的一行还是不换行。使用do关键字能专注于异常处理的范围，do-while循环变成了repeat-while。同样的，许多协议的命名也发生了改变，例如Printable变成了CustomStringConvertible。

迁移 - 有这么多的小的语法改变，你要怎么对代码库进行更新喃？从Swift1到2的迁移将会帮我们把东西升级成最新的标准以及语法的改变。这个迁移甚至能智能的使用新的异常处理升级的你的代码以及升级的你的文档库评论到新的格式风格！

开源！- 对我们来说最振奋的新闻就是Swift2将在秋季进行开源。似乎这将使Swift不仅仅只是iOS开发者使用，学习Swift也变得越来越重要。另外，这将是一个了解底层甚至为开源贡献并将你的名字写在Swift编译历史中的巨大机会。;]

# 之后做什么？

这些只是在Swift发布东西中选取了一些，想看全部的细节可以查看[WWDC session videos](https://developer.apple.com/videos/wwdc2015/)和更新好了的[Swift Programming Language book](https://itunes.apple.com/us/book/swift-programming-language/id1002622538?mt=11&uo=8&at=11ld4k)。

如果还有人记得Swift最初的beta版和1.0发布之后的体积变化，这次体积当然也会很大的变化。我们团队将持续前沿更新，所以随时关注我们的教程，书籍公告，和视频吧，我们将带来更激动人心的改变。

对于Swift2你最期待的是什么？你希望我们最快更新的是什么？在评论区中留下你们的想法！

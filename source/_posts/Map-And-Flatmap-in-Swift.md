title: Swift 中 map & flatmap 的使用
categories: Swift
date: 2016-08-23 22:37:48
tags:
description: Swift 中 map & flatmap 的使用
---

有很多大神的博文讲解了 `map & flatmap` 的使用了，本文是看完各位大神讲解之后，自己梳理的一篇小总结。

<!--more-->

## 数组里的使用

先来看看数组里面的 `map & flatmap` 的定义：
    
    extension SequenceType {
    	public func map<T>(@noescape transform: (Self.Generator.Element) throws -> T) rethrows -> [T]
	 
    	public func flatMap<T>(@noescape transform: (Self.Generator.Element) throws -> T?) rethrows -> [T]
    
    	public func flatMap<S : SequenceType>(transform: (Self.Generator.Element) throws -> S) rethrows -> [S.Generator.Element]
	}
	 

对于 `map` 我就不再过多解释了，它可以将数组元素依次映射成对应的值。而对应 `flatmap` 我们可以看到，他有两种定义。对于第一种来说，意思是如果 `T` 是 `optional`，用 `map` 映射之后的数组，是`optional` 的数组，用 `flatMap` 隐射之后的数组是可选解析之后的数组，示例代码如下：


	let testArray = ["test1","test1234","","test56"]

	let anotherArray = testArray.map { (string: String) -> Int? in
	    let length = string.characters.count
	    guard length > 0 else {
	        return nil
	    }
	    return string.characters.count
	}

	print(anotherArray)
	// [Optional(5), Optional(8), nil, Optional(6)]\n


	let anotherArray2 = testArray.flatMap { (string: String) -> Int? in
	    let length = string.characters.count
	    guard length > 0 else {
	        return nil
	    }
	    return string.characters.count
	}
	print(anotherArray2)
	// [5, 8, 6]\n


这种可以应用在过滤 `nil`，过滤异常数据上面，也能通过 `filter + map` 来实现。

`flatmap` 的第二种形式，是指当前集合的每个元素都映射成数组类型，然后再将映射完的每个数组，取出值组成新的数组。来看看例子：

	let numbersCompound = [[1,2,3], [4,5,6]]
	var res = numbersCompound.map{$0.map{$0 + 2}}

	print(res) // [[3, 4, 5], [6, 7, 8]]

	var flatRes = numbersCompound.flatMap{$0.map{$0 + 2}}
	print(flatRes) // [3, 4, 5, 6, 7, 8]

可以看到 `flatmap` 有降维的效果，会将一个集合的所有元素，添加到另一个集合。


## 可选里的使用

这是 `Swift` 里面可选的 `map&flatmap` 方法定义

	public enum Optional<Wrapped> : _Reflectable, NilLiteralConvertible {
	
		case None
		case Some(Wrapped)

    	public func map<U>(@noescape f: (Wrapped) throws -> U) rethrows -> U?
    
    	public func flatMap<U>(@noescape f: (Wrapped) throws -> U?) rethrows -> U?
    
	}

可以看到 `map` 方法适应于转换时没有返回 `nil` 的时候，`flatmap` 更适应于闭包返回值有 `nil` 的时候。使用 `map` 处理`optional` 的好处在于，不用去判断 `optional` 是否为 `nil`，例如：

	let date: NSDate? = NSDate()
	let formatter = NSDateFormatter()
	formatter.dateFormat = "YYYY-MM-dd"
	let formatted = date.map(formatter.stringFromDate)
	
	let s: String? = "abc"
	let v = s.flatMap { (a: String) -> Int? in
	    return Int(a)
	}   

我们再来想想，如果 `map` 和 `flatmap` 反着来用会有什么影响？我们将上面的代码 `map` 和 `flatmap` 互换一下：

	let date: NSDate? = NSDate()
	let formatter = NSDateFormatter()
	formatter.dateFormat = "YYYY-MM-dd"
	let formatted = date.flatmap(formatter.stringFromDate)
	
这样还是能得到正确的值，`formatted` 的类型是 `String？`

	let s: String? = "abc"
	let v = s.map { (a: String) -> Int? in
	    return Int(a)
	}   

而这样得到的v的类型却是 `Int??` 这会使得 `if let` 的可选解析失效。

这是因为也就是说对于 `map`，是将你可选解析的值映射成普通值，再封装一层可选，对于 `flatmap`，则是直接将可选解析的值映射成可选。虽然最后得到的都是可选值，但是来的方式不一样，所以如果使用不慎就会出现多重 `optional` 的情况。

## Monad

`map & flatmap` 是抽象的计算过程，一个封装的类型只要支持 `map`，即将未封装的值转换成未封装的值，就是 `Functor`；如果支持`flatmap`，即从未封装的值到封装的值就是 `Monad`。来看看示例代码:

	// 基于 MONAD 处理错误的强大机制
	enum Result<Value> {
	    case Failure(ErrorType)
	    case Success(Value)
	}

	extension Result {
	    
	    func flatMap<T>(@noescape transform: Value throws -> Result<T>) rethrows -> Result<T>  {
	        switch self {
	        case let .Failure(error):
	            return .Failure(error)
	        case let .Success(value):
	            return try transform(value)
	        }
	    }
	    
	    func map<T>(@noescape transform: Value throws -> T) rethrows -> Result<T> {
	        
	        switch self {
	        case let .Failure(error):
	            return .Failure(error)
	        case let .Success(value):
	            return try .Success(transform(value))
	        }
	    }
	}

基于这个错误类型，我们可以写一系列的函数来处理图片，在使用上就可以达到链式的效果。

	func toImage(data: NSData) -> Result<UIImage>
	func addAlpha(image: UIImage) -> Result<UIImage>
	func roundCorner(image: UIImage) -> Result<UIImage>
	func applyBlur(image: UIImage) -> Result<UIImage>
	 
	toImage(data)
	.flatMap(addAlpha)
	.flatMap(roundCorner)
	.flatMap(applyBlur)

简单的来说，`Monad` 是一个封装了计算过程的容器，可以基于 `Monad` 做一些流式开发。

### 参考资料：

[Swift函数编程实战（包涵卿）](http://www.imooc.com/video/11107)

[Swift 烧脑体操（四）- map 和 flatmap](http://blog.devtang.com/2016/03/05/swift-gym-4-map-and-flatmap/)

[谈谈 Swift 中的 map 和 flatMap](http://swiftcafe.io/2016/03/28/about-map/)

[Swift：map 和 flatMap 基础入门](http://swift.gg/2015/11/26/swift-map-and-flatmap/) 

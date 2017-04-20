---
layout: post
#标题配置
title:  Categories、Extensions 那点事儿~
#时间配置
date:   2014-4-27 00:00:00 +0800
#大类配置
categories: document
#小类配置
tag: 学习笔记
---

* content
{:toc}


最近乱点了篇博客关于分类和类扩展， 博主总结得乱七八糟，虽然这知识点太过久远，还是我来谈谈iOS中Categories（分类）、Extensions（类扩展）和继承的区别 吧。

简单的讲，通过Categories（分类）即使在没有某个系统类源代码（iOS不开源的）的情况下，也可以为这个类添加新的方法声明。而新方法的实现可以在另外的文件中。

其语法举例如下：



[objc] view plaincopy

1.	#import "ClassName.h"  
2.	  
3.	   
4.	  
5.	@interface ClassName ( CategoryName )   
6.	  
7.	// method declarations   
8.	  
9.	@end  


这样我们可以扩展出新的方法，这在我们实际开发中很常用。但要注意Category只能用于添加方法，不能用于添加成员变量。

理解了Category，Extension就不难理解了。Extension是Category的一个特例，其名字为匿名（为空），并且新添加的方法一定要予以实现。（Category没有这个限制）

下面这里是翻译的objective-c-primer.pdf 中的相关资料，可以对这两个概念有一个初步了解。

Categories and Extensions 


Categories
catgory 允许你为一个已经存在的类增加方法----甚至是一个你没有source的类。
Categories是一种强大的特性，它允许你直接扩展类的功能，而不需要使用子类的方法来扩展。
使用categories，你可以把你自己的类的实现方法分布在几个不同的文件里。
Class extensions与此相似，但是它允许在@ implementation代码块额外的增加自己需要的APIs，而不是在原始类的@interface代码块里, extension中声明的方法是私有的，只有主implement能调用，外部的类无法调用。

除此之外，你不能使用category来为一个类增加额外的实例变量。

Categories and 继承


category 增加的这些方法的会成为类类型的一部分。例如，编译器会认为这些通过category的方式增加到NSArray类里面的方法就是NSArray实例中的一部分。
而那些通过继承NSArray的方式，增加到NSArray子类里的方法则不会被包含在NSArray类型里。
Category methods可以做任何在类中正常定义的方法能做的事。在运行时，没有任何区别。通过category 增加到类中的方法会被这个类的所有子类继承，就和此类的其它方法一样。

Category的声明看赶来和一个interface的声明非常相似(除了category的名字要列在类名后的括号里和没有指明超类之外)。除非它的方法不会访问任何类的实例变量（Category可以包含类的所有成员变量，即使是私有的），否则category必须import它扩展的类的文件里来，如下：

[objc] view plaincopy

1.	#import  "ClassName.h "  
2.	  
3.	   
4.	  
5.	@interface ClassName ( CategoryName )  
6.	  
7.	//method declarations  
8.	  
9.	@end  


为一个类增加categories的数量是没有限制的，但是每一个category 的名字必须要是不相同的，而且应该声明和定义一个不同的方法集。
Category 增加的方法如果与类的方法同名，会覆盖原类的方法，因为Category的优先级更高！所以用Category重载继承的类的方法是非常危险的！
虽然Objective-C语言目前允许使用category来通过重载继承的类的方法或者甚至是类文件中的方法，但是这种做法是被强烈反对的。category不是子类的替代品。使用category 来重载方法有很多重大的缺陷：


当category 重载一个从父类继承过来的方法，通常可以通过super关键字来调用父类的实现方法。然而，如果category重载一个扩展类本身存在的方法，就没有唤醒原始实现方法的办法了。



同一个类的category不能声明重载这个类的另一个category中声明的方法。这一点非常的重要，因为很多Cocoa类也是通过使用categories来实现的。一个你试图重载的框架中定义的方法可能本身就已经在一个category被实现了，如果你这样做了，很可能使用得前面的category的方法的实现失效。



一些category methods的存在可能会导致整个框架的行为发生变化。例如，如果你在NSObject的一个category中重载windowWillClose:委托方法，在你的程序里所有窗口的委托将会使用category方法来回应；所有NSWIndow实例的行为都会改变。你为一个框架类增加的Categories可能会导致行为上很神秘的变化和程序的崩溃。

所以，一句话就是且用且珍惜！

Extensions
除了它所声明的方法必须要在相应类的主要@implementation代码块被实现以外，类的extensions就像一个匿名的categories。

例如，下面代码里的声明和实现在编译器里并不会报错，甚至categories中setNumber: 方法没有实现
也不会有错：

[objc] view plaincopy

1.	@interface MyObject : NSObject  
2.	  
3.	{  
4.	  
5.	NSNumber * number;  
6.	  
7.	}   
8.	  
9.	   
10.	  
11.	- (NSNumber *)number;  
12.	  
13.	@end  
14.	  
15.	   
16.	  
17.	@interface MyObject ( Setter )  
18.	  
19.	- (void)setNumber : (NSNumber *)newNumber;  
20.	  
21.	@end  
22.	  
23.	   
24.	  
25.	@implementation MyObject  
26.	  
27.	   
28.	  
29.	- (NSNumber *)number  
30.	  
31.	{  
32.	  
33.	return number;  
34.	  
35.	}  
36.	  
37.	@end  


extensions允许你在本地为一个类声明额外需要的方法，而不需要在原始类的@interface代码块去添加，正如下面的例子所示：

[objc] view plaincopy

1.	@interface MyObject : NSObject  
2.	  
3.	{  
4.	  
5.	NSNumber * number;  
6.	  
7.	}  
8.	   
9.	  
10.	- (NSNumber * )number;  
11.	  
12.	@end  
13.	  
14.	   
15.	  
16.	@interface MyObject()  
17.	  
18.	- (void)setNumber: (NSNumber *)newNumber;  
19.	  
20.	@end  
21.	  
22.	   
23.	  
24.	@implementation MyObject  
25.	  
26.	   
27.	  
28.	- (NSNumber *)number  
29.	  
30.	{  
31.	  
32.	return number;  
33.	  
34.	}  
35.	  
36.	   
37.	  
38.	- (void)setNumber:(NSNumber *)newNumber  
39.	  
40.	{  
41.	  
42.	number = newNumber;  
43.	  
44.	}  
45.	  
46.	@end  


setNumber: 方法的实现必须得在类的主@implementation代码块里(你不能在category里实现它)。


总结：




相同点：

都可以为类添加一个额外的方法。

不同点：
1、

Categories在@implementation中不提供实现，编译器不会报错，运行调用时出错；

Extensions在@implementation中不提供实现，编译器警告；

继承 子类在@implementation中不提供实现，编译器不会报错，运行调用时出错。

2、

Category只能用于添加方法，不能用于添加成员变量。
extension中声明的方法和添加的成员变量是私有的，只有主implement能调用，外部的类无法调用。

3、

category 增加的这些方法的会成为类类型的一部分；

继承增加的方法不会成为父类的一部分。

4、



Category 增加的方法如果与类的方法同名，会覆盖原类的方法，因为Category的优先级更高！

继承中子类也会覆盖父类方法，相似。

Extensions则会冲突报错。

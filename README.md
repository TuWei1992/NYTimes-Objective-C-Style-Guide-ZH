# 纽约时报移动团队 Objective-C 风格指南

## 背景介绍

这里有一些苹果提供的风格指南，如果某些东西没在这里提到，那它可能被这里其中一个更详细的所涵盖了：   

* [Objective-C 编程语言][Introduction_1]
* [Cocoa 基本原理指南][Introduction_2]
* [Cocoa 编码指南][Introduction_3]
* [iOS 应用编程指南][Introduction_4]

[Introduction_1]:http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html

[Introduction_2]:https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html

[Introduction_3]:https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html

[Introduction_4]:http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html


## 目录

* [点语法](#点语法)
* [间距](#间距)
* [条件判断](#条件判断)
	* [三目运算符](#三目运算符)
* [错误处理](#错误处理)
* [方法](#方法)
* [变量](#变量)
* [命名](#命名)
	* [下划线](#下划线)
* [注释](#注释)
* [Init 和 Dealloc](#init-和-dealloc)
* [字面量](#字面量)
* [CGRect 函数](#CGRect-函数)
* [常量](#常量)
* [枚举类型](#枚举类型)
* [私有属性](#私有属性)
* [图片命名](#图片命名)
* [布尔](#布尔)
* [单例](#单例)
* [Xcode 工程](#Xcode-工程)

## 点语法

应该**始终**使用点语法来访问或者修改属性，访问其他实例变量时首选括号。   

**推荐：**   
```objc
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

**反对：**
```objc
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```

## 间距

* 一个缩进使用4个空格，永远不要使用制表符（tab）缩进。请确保在 Xcode 中设置此偏好。
* 方法的大括号和其他的大括号（`if`/`else`/`switch`/`while` 等等）始终和声明在同一行开始，在新的一行结束。

**推荐：**   
```objc
if (user.isHappy) {
//Do something
}
else {
//Do something else
}
```   
* 方法之间应该正好空一行，这有助于视觉清晰度和组织性。在方法中的功能块之间应该使用空白分开，但往往可能有新的方法。
* `@synthesize` 和 `@dynamic` 在 implementation 中每个都应该占一个新行。


## 条件判断

条件判断主体部分应该始终使用大括号括住来防止[出错][Condiationals_1]，即使它可以不用大括号（例如它只需要一行）。这些错误包括添加第二行（代码）并希望它是 if 语句的一部分时。还有另外一种[更危险的][Condiationals_2]，当 if 语句里面的一行被注释掉，下一行就会在不经意间成为了这个 if 语句的一部分。此外，这种风格也更符合所有其他的条件判断，因此也更容易检查。    

**推荐：**     
```objc
if (!error) {
    return success;
}
```

**反对：**
```objc
if (!error)
    return success;
```

或

```objc
if (!error) return success;
```


[Condiationals_1]:(https://github.com/NYTimes/objective-c-style-guide/issues/26#issuecomment-22074256)
[Condiationals_2]:http://programmers.stackexchange.com/a/16530

### 三目运算符

三目运算符，? ，只有当它可以增加代码清晰度或整洁时才使用。单一的条件都应该优先考虑使用。多条件时通常使用 if 语句会更易懂，或者重构为实例变量。   

**推荐：**
```objc
result = a > b ? x : y;
```

**反对：**
```objc
result = a > b ? x = c > d ? c : d : y;
```

## 错误处理

当引用一个返回错误参数（error parameter）的方法时，应该针对返回值，而非错误变量。   

**推荐：**
```objc
NSError *error;
if (![self trySomethingWithError:&error]) {
    // 处理错误
}
```

**反对：**
```objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
    // 处理错误
}
```
一些苹果的 API 在成功的情况下会写一些垃圾值给错误参数（如果非空），所以针对错误可以造成虚假结果（和接下来的崩溃）。

## 方法

在方法签名中，在 -/+ 符号后应该有一个空格。方法片段之间也应该有一个空格。   

**推荐：**:
```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
```

## 变量

变量名字应该尽可能地描述清楚意思。除了 `for()` 循环外，其他情况都应该避免使用单字母的名字。   
星号表示指针属于变量，例如：`NSString *text` 不要写成 `NSString* text` 或者 `NSString * text` ，常量除外。   
尽量定义属性来代替直接使用实例变量。除了初始化方法（`init`， `initWithCoder:`，等）， `dealloc` 方法和自定义的 setters 和 getters 内部，应避免直接访问实例变量。参见[这里][Variables_1]。


**推荐：**

```objc
@interface NYTSection: NSObject

@property (nonatomic) NSString *headline;

@end
```

**反对：**

```objc
@interface NYTSection : NSObject {
    NSString *headline;
}
```
   
[Variables_1]:https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW6

## 命名

只要有可能，尽量遵守苹果命名约定，尤其那些涉及到[内存管理规则][Naming_1]，（[NARC][Naming_2]）的。

长点的方法名和变量名都不错。

**推荐：**

```objc
UIButton *settingsButton;
```

**反对：**

```objc
UIButton *setBut;
```
类名和常量应该一直使用三个字母的前缀（例如 `NYT`），但 Core Data 实体名称可以省略。为了代码清晰，常量应该使用相关类的名字作为前缀并使用驼峰命名法。   

**推荐：**

```objc
static const NSTimeInterval NYTArticleViewControllerNavigationFadeAnimationDuration = 0.3;
```

**反对：**

```objc
static const NSTimeInterval fadetime = 1.7;
```

属性应该使用驼峰命名法并且开头字母为小写。**如果 Xcode 可以自动合成变量，那就让它自动合成。**否则，为了保持一致，这些属性相对应的实例变量应该使用驼峰命名法命名，并且首字母小写，以下划线开头。这和 Xcode 默认的样式保持一致。   

**推荐：**

```objc
@synthesize descriptiveVariableName = _descriptiveVariableName;
```

**反对：**

```objc
id varnm;
```

[Naming_1]:https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html

[Naming_2]:http://stackoverflow.com/a/2865194/340508

### 下划线

当使用属性的时候，应当始终使用 `self.` 来访问实例变量。这样所有的属性看起来就很清晰，因为它们都是以 `self.` 开头。局部变量不应该包含下划线。   

## 注释

当需要的时候，注释应该用来解释**为什么**一段特定的代码做了一些事情。任何注释必须保持最新或者删除掉。   

通常应该避免一块注释，代码就应该尽量作为自身的文档，只需要隔几行写几句说明。这并不适用于用来生成文档的注释。   


## init 和 dealloc

`dealloc` 方法应该放在实现文件的最上面，正好在 `@synthesize` 和 `@dynamic` 语句的后面。在任何类中，`init` 都应该直接放在 `dealloc` 方法的下面。   

`init` 方法的结构应该像这样：   

```objc
- (instancetype)init {
    self = [super init]; // 或者调用指定的初始化方法
    if (self) {
        // Custom initialization
    }

    return self;
}
```

## 字面量

每当创建 `NSString`， `NSDictionary`， `NSArray`，和 `NSNumber` 类的不可变实例时，都应该使用字面量。要注意 `nil` 值不能传给 `NSArray` 和 `NSDictionary` 字面量，这样做会导致崩溃。   

**推荐：**

```objc
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone" : @"Kate", @"iPad" : @"Kamal", @"Mobile Web" : @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;
```

**反对：**

```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingZIPCode = [NSNumber numberWithInteger:10018];
```

## CGRect 函数

当访问一个 `CGRect` 的 `x`， `y`， `width`， `height` 时，应该使用[`CGGeometry` 函数][CGRect-Functions_1]代替直接访问结构体成员。苹果的 `CGGeometry` 参考中说到：

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**推荐：**

```objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
```

**反对：**

```objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
```

[CGRect-Functions_1]:http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html

## 常量

常量首先内联字符串字面量或数字，因为他们常用的变量可以轻易重用并且可以快速改变而不需要找到然后替换。常量应该声明为 `static` 常量而不是 `#define` ，除非非常明确地要当做宏来使用。   

**推荐：**

```objc
static NSString * const NYTAboutViewControllerCompanyName = @"The New York Times Company";

static const CGFloat NYTImageThumbnailHeight = 50.0;
```

**反对：**

```objc
#define CompanyName @"The New York Times Company"

#define thumbnailHeight 2
```

## 枚举类型

当使用 `enum` 时，建议使用新的固定基础类型规范，因为它具有更强的类型检查和代码补全功能。现在 SDK 包含了一个宏来增加便捷性以促进使用固定基础类型 - `NS_ENUM()`   

**推荐：**

```objc
typedef NS_ENUM(NSInteger, NYTAdRequestState) {
    NYTAdRequestStateInactive,
    NYTAdRequestStateLoading
};
```

## 私有属性

私有属性应该声明在类实现文件的延展（匿名的类目）中。有名字的类目（例如 `NYTPrivate` 或 `private`）永远都不应该使用，除非要扩展其他类。   

**推荐：**

```objc
@interface NYTAdvertisement ()

@property (nonatomic, strong) GADBannerView *googleAdView;
@property (nonatomic, strong) ADBannerView *iAdView;
@property (nonatomic, strong) UIWebView *adXWebView;

@end
```

## 图片命名

图片名称命名应始终保持组织与开发稳健。他们应该使用驼峰命名法来描述它们的意图，其次是要自定义的类或属性的无前缀名字（如果有要自定义的话），最后进一步说明颜色 和/或 展示位置，和它们最终的状态。   

**推荐：**

* `RefreshBarButtonItem` / `RefreshBarButtonItem@2x` 和 `RefreshBarButtonItemSelected` / `RefreshBarButtonItemSelected@2x`
* `ArticleNavigationBarWhite` / `ArticleNavigationBarWhite@2x` 和 `ArticleNavigationBarBlackSelected` / `ArticleNavigationBarBlackSelected@2x`.

图片目录里的多张类似用途的图片应该分类到各自的组里。   


## 布尔

自从 `nil` 可以作为 `NO` 使用之后，已经没有必要在条件中判断它。永远不要拿某个东西直接和 `YES` 比较，因为 `YES` 被定义为 1，而 `BOOL` 可以多达 8 位。

这允许更多一致性的文件和更大的视觉清晰度。   

**推荐：**

```objc
if (!someObject) {
}
```

**反对：**

```objc
if (someObject == nil) {
}
```

-----

**对于 `BOOL` 来说, 这有两种用法:**

```objc
if (isAwesome)
if (![someObject boolValue])
```

**反对：**

```objc
if ([someObject boolValue] == NO)
if (isAwesome == YES) // Never do this.
```

-----

如果一个 `BOOL` 属性名称是一个形容词，属性可以省略 “is” 前缀，但指定 get 访问器惯用的名字，例如：   

```objc
@property (assign, getter=isEditable) BOOL editable;
```

文字和例子来自 [Cocoa 命名指南][Booleans_1] 。

[Booleans_1]:https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE


## 单例

单例对象应该使用线程安全的方式创建共享的实例。   

```objc
+ (instancetype)sharedInstance {
   static id sharedInstance = nil;

   static dispatch_once_t onceToken;
   dispatch_once(&onceToken, ^{
      sharedInstance = [[self alloc] init];
   });

   return sharedInstance;
}
```
这将会预防[有时可能产生的许多崩溃][Singletons_1]。   

[Singletons_1]:http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html


## Xcode 工程

为了避免文件杂乱无序，物理文件应存放在 Xcode 项目文件同步。Xcode 创建的任何组都必须在文件系统有相应的映射。代码应该不仅按类型分组,而且功能更清晰。   


如果可能的话，尽可能一直打开 target Build Settings "Treat Warnings as Errors" 以及一些[额外的警告][Xcode-project_1]。如果你需要忽略指定的警告,使用 [Clang 的编译特性][Xcode-project_2] 。


[Xcode-project_1]:http://boredzo.org/blog/archives/2009-11-07/warnings

[Xcode-project_2]:http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas


# 其他 Objective-C 代码风格 用户指南

如果感觉我们的不太符合你的口味，可以看看下面的代码风格：

* [Google](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml)
* [GitHub](https://github.com/github/objective-c-conventions)
* [Adium](https://trac.adium.im/wiki/CodingStyle)
* [Sam Soffes](https://gist.github.com/soffes/812796)
* [CocoaDevCentral](http://cocoadevcentral.com/articles/000082.php)
* [Luke Redpath](http://lukeredpath.co.uk/blog/my-objective-c-style-guide.html)
* [Marcus Zarra](http://www.cimgf.com/zds-code-style-guide/)

























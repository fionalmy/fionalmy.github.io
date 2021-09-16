As we all know, 'Masonry' is the most popular third-part Automatic Layout Framework. A more interesting thing is that some new developers don't know much about NSLayout Constraint, in their opinion, 'Masonry' equals 'Auto Layout' in iOS/OSX.😄

This article mainly focus on practice. If you are a Autolayout beginner, or you want to know how to construct contraints for your views, this article will help you.

## Background

First of all, you'd better keep in mind that 'Autolayout' is a totally diferrent layout compared with 'Frame based layout'.

As for 'Frame based layout', you should set an exact frame for a view. The basis of 'Frame based layout' is **coordinate**. You should calculate where your views be put in the coordinate. In the following picuture, the blue view in the coordinate, whose x is 3cm, y is 2cm, width is 10cm, and the height is 11cm.

![截屏2021-09-15 下午3.51.08](/Users/liumengyu/Desktop/截屏2021-09-15 下午3.51.08.png)

However, as for 'Masonry', you could describe the constraints related to a view. The basis of 'Masonry' is **relationship** (most articles says 'constraints', but I think 'relationship' is easier and clear to understand). You should decide how your views relate with other views. As the following picture shows, the same blue view in a gray view, whose left is greater than the gray one with 3cm offset, top is greater than the gray one with 2cm offset, the width is less than the gray one with -5cm, and the height is less than the gray one with -5cm.

![截屏2021-09-15 下午3.51.23](/Users/liumengyu/Desktop/截屏2021-09-15 下午3.51.23.png)

## Differentce

Let's see following codes about the above blue view using 'Masonry' and 'Frame based layout':

```objective-c
// Masonry
[blueView mas_makeConstraints:^(MASConstraintMaker *make) {
    make.left.equalTo(grayView).offset(3.0f);
  	make.top.equalTo(grayView).offset(2.0f);
  	make.width.equalTo(grayView).offset(-5.0f);
  	make.height.equalTo(grayView).offset(-5.0f);
}];
```

```objective-c
// Frame based layout
blueView.frame = CGRectmake(3.0f, 2.0f, 10.0f, 11.0f);
```

`You could easily find that the code in 'Masonry' all described the **relationship** with the gray view. However, the code in 'Frame based layout' states the exact frame in **coordinate**.



## Introduction

Masonry is a open source project, you could find it on Github: [Masonry](https://github.com/SnapKit/Masonry). 

The most remarkable character of 'Masonry' is 'a domain specific language(DSL)', which is a typical implement of 'a chain programming approach' in Objective-C and Swift. 

'A chain programming approach' us a approach means you could consistently call a function, and 'a domain specific language(DSL)' is a convient way to call a function in OC/Swift. 

For example, 'I go to market to buy some flowers to embellish my room.

```objective-c
// pseudo-code
self.goToMarket.buyFlowers.embellishRoom
```

> make.left.equalTo(grayView).offset(3.0f); 

is a typical 'chain programming approach' with some domains, right?

## Basic Usage

### 1. Installation

you should add 'Masonry' into your project, you could user [Cocoapods](https://github.com/CocoaPods/CocoaPods), and In your Podfile

pod 'Masonry'

### 2. Common Usage

```objective-c
// Masonry
[blueView mas_makeConstraints:^(MASConstraintMaker *make) {
    make.left.equalTo(grayView.mas_left).offset(3.0f);
  	make.top.equalTo(grayView.mas_right).offset(2.0f);
  	make.width.equalTo(grayView.mas_width).offset(-5.0f);
  	make.height.equalTo(grayView.mas_height).offset(-5.0f);
}];
```

you could use a masonry block like the code showed above for a view to concentrate its relationship with others. 

Do you feel it's a bit verbose? Ok, let's optimize it together.

![截屏2021-09-15 下午4.53.21](/Users/liumengyu/Desktop/截屏2021-09-15 下午4.53.21.png)

```objective-c
[yellowView mas_makeConstraints:^(MASConstraintMaker *make) {
    make.left.equalTo(blueView.mas_left).offset(1.0f);
  	make.top.equalTo(blueView.mas_right).offset(1.0f);
  	make.width.equalTo(blueView.mas_width).offset(-2.0f);
  	make.height.equalTo(blueView.mas_height).offset(-2.0f);
}];
```

1. If the attributes of two views are the same, you could drop the second attribute

   ```objective-c
   make.left.equalTo(blueView).offset(1.0f);
   ```

2. If two attributes have same offset, you could merge them together

   ```objective-c
   make.left.top.equalTo(blueView).offset(1.0f);
   ```

3. If you are describing the constraints between a view and its superview, you could drop the superview statement

   ```
   // the object for equalTo must be a NSObject, so you should use NSNumber
   make.left.top.equalTo(@(1.0f));
   // or
   // 'mas_equalTo' is mainly used for floatValue
   make.left.top.mas_equalTo(1.0f);
   ```

   Then the code become shorter:

   ```objective-c
   [yellowView mas_makeConstraints:^(MASConstraintMaker *make) {
       make.left.top.mas_equalTo(1.0f);
       make.width.height.mas_equalTo(-2.0f);
   }];
   ```

4. Think about the relative relation instead of the absolute relation, you could change the code furthermore

   ```objective-c
   [yellowView mas_makeConstraints:^(MASConstraintMaker *make) {
       make.centerX.centerY.equalTo(blueView);
     	// make.centerX.centerY.mas_equalTo(0.0f);
       make.width.height.mas_equalTo(-2.0f);
   }];
   ```

5. The key point when you use 'Masonry' is to find out the<font color=Red> crucial relationship</font>, if you want to identify the constraints most clear and concise.

   ```objective-c
   [yellowView mas_makeConstraints:^(MASConstraintMaker *make) {
       make.edges.equalTo(blueView).offset(-1.0f);
   }];
   // or
   [yellowView mas_makeConstraints:^(MASConstraintMaker *make) {
       make.edges.mas_equalTo(-1.0f);
   }];
   ```

### 3. More helpful function

- #### greaterThanOrEqualTo / lessThanOrEqualTo

  ```objective-c
  // label should be longer than 100pt, and could not be longer than its super view
  [label mas_makeConstraints:^(MASConstraintMaker *make) {
      make.width.lessThanOrEqualTo(superView);
    	make.width.greaterThanOrEqualTo(@100);
  }];
  ```

- #### priority

  ```objective-c
  // rightView should be on leftView's right, but if leftView disappear, rightView should be on superView with 10pt left margin
  [rightView mas_makeConstraints:^(MASConstraintMaker *make) {
      make.left.equalTo(leftView.mas_right).with.priorityHigh();
    	make.left.equalTo(superView).offset(10.0f).with.priorityLow();
  }];
  
  // using priority to identify the exact value of priority, default is 1000
  make.top.equalTo(label.mas_top).with.priority(100)
  ```

- #### Fixed width / fixed space

  ```objective-c
  // all views in subViewArray are in a line with fixed space 10 between themselves, left space 1, and right space 2
  [self.subViewArray mas_distributeViewsAlongAxis:MASAxisTypeHorizontal withFixedSpacing:10 leadSpacing:1 tailSpacing:2];
  
  // all views in subViewArray are in a line with the same fixed width 50, left space 1, and right space 2
  [self.masonryViewArray mas_distributeViewsAlongAxis:MASAxisTypeHorizontal withFixedItemLength:50 leadSpacing:1 tailSpacing:2];
  ```

### 4. Time

After you master how to set constraints, you may have another confuse: when should you add constraints for a view?

The Masonry's readme and most articles figure out that Apple recommend developer put 'adding constraints' code in a function called 'updateConstraints'. But when you see the ['updateConstraints' documentation](https://developer.apple.com/documentation/uikit/uiview/1622512-updateconstraints?language=objc), there is a note:

> It is almost always cleaner and easier to update a constraint immediately after the affecting change has occurred. For example, if you want to change a constraint in response to a button tap, make that change directly in the button’s action method.
>
> You should only override this method when changing constraints in place is too slow, or when a view is producing a number of redundant changes.

So Apple means you'd better add constraints immediately when you change the UI. For example, youd could add constraints after you add your view on its super view. This sights could be proved by [Apple's Auto Layout Guide](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/LayoutUsingStackViews.html#//apple_ref/doc/uid/TP40010853-CH11-SW1) 

```objective-c
@implementation DemoView

- (id)init {
    self = [super init];
    if (self) {
      	[self setupSubviews];
    }
    return self;
}

- (void)setupSubviews {
    self.button = [[UIButton alloc] init];
  	[self addSubview:self.button];
    [subView mas_makeConstraints:^(MASConstraintMaker *make) {
        make.left.top.equalTo(@0);
        make.width.height.equalTo(@10);
     }];
}

@end
```

### 5. Change constraints

If you want to change the button's contraints after clicking it.

```objective-c
@implementation DIYCustomView

......
  
- (void)setupSubviews {
    self.button = [[UIButton alloc] init];
    [self.button addTarget:self action:@selector(didClickButton:) forControlEvents:UIControlEventTouchUpInside];
  	[self addSubview:self.button];
    [subView mas_makeConstraints:^(MASConstraintMaker *make) {
        make.left.top.equalTo(@0);
        make.width.height.equalTo(@10);
    }];
}

- (void)didClickButton:(UIButton *)button {
    [subView mas_remakeConstraints:^(MASConstraintMaker *make) {
        make.left.top.equalTo(@0);
        make.width.height.equalTo(@10);
     }];
}

@end
```

You may notice that I used 'remake' instead of 'make'. Actually, there are three approaches:

1. **mas_makeConstraints**: common approach to set constraints

2. **mas_updateConstraints**

   You could use this function in three ways:

   ```objective-c
   [subView mas_makeConstraints:^(MASConstraintMaker *make) {
       make.left.top.equalTo(@0);
       make.width.height.equalTo(@10);
   }];
   
   // change the offset for a already existed constraint
   [subView mas_updateConstraints:^(MASConstraintMaker *make) {
       make.top.equalTo(@100);
   }];
   
   // add some totaly new constraints
   [subView mas_updateConstraints:^(MASConstraintMaker *make) {
       make.top.equalTo(@100);
   }];
   
   // change the refer view for a already existed constraint, if your constraints are complex, the following change will sometimes not take effect or crash when have conficts
   [subView mas_updateConstraints:^(MASConstraintMaker *make) {
       make.top.equalTo(topView.mas_bottom);
   }];
   ```

3. **mas_remakeConstraints**: remove all existed constraints, and add new constraints. You'd better use it when you set constraints for subviews in a cell or other views will change layout consistently.

## Tips

1. You could only add constraints after you add the view on its super view
2. Constraints should be pairs. For example, you should remember add bodth width and height for a view. Besides, you can not make a 'width constraint' confict with the 'left and right constraints'.



## Mix with 'Frame Based Layout'

1. You'd better not mix 'Auto layout' and 'Frame based layout'

2. If neccessay, you should keep in mind that 'Auto layout' will take layout immediately.

   ```objective-c
   self.referView = [[UIView alloc] init];
   [self.view addSubview:self.referView];
   [self.referView mas_makeConstraints:^(MASConstraintMaker *make) {
   		make.left.top.equalTo(@30);
   		make.width.height.equalTo(@100);
   }];
   
   // test view will not get the correct y
   CGFloat y = self.referView.frame.origin.y + self.referView.frame.size.height;
   self.testView = [[UIView alloc] initWithFrame:CGRectMake(0, y, 100, 100)];
   [self.view addSubview:self.testView];
   
   // you could add the following two lines code after add constraints
   [self.view setNeedsLayout];
   [self.view layoutIfNeeded];
   ```

   With mthod 'layoutIfNeeded', you could update the layout immediately. As for the 'setNeedsLayout', Apple said that

   > You can call this method to indicate that the layout of a layer’s sublayers has changed and must be updated. The system typically calls this method automatically when the layer’s bounds change or when sublayers are added or removed.

   So you could call 'setNeedsLayout' before layoutIfNeeded to make sure layout could update immediately.

   ## Animation

Since the time you call 'layoutIfNeeded' could effect when the layout will update, you could add animations for a view with 'layoutIfNeeded'.

```objective-c
@interface ViewController ()
@property (nonatomic, strong) UIButton *button;
@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    [self setupSubviews];
}

- (void)setupSubviews {
    self.view.backgroundColor = [UIColor whiteColor];
    
    self.button = [[UIButton alloc] init];
    self.button.backgroundColor = [UIColor redColor];
    [self.view addSubview:self.button];
    [self.button addTarget:self action:@selector(clickButton:) forControlEvents:UIControlEventTouchUpInside];
    [self.button mas_makeConstraints:^(MASConstraintMaker *make) {
        make.left.top.equalTo(@30);
        make.width.height.equalTo(@100);
    }];
}

- (void)clickButton:(id)sender {
    [self.button mas_updateConstraints:^(MASConstraintMaker *make) {
        make.top.equalTo(@100);
    }];

    [UIView animateWithDuration:3 animations:^{
        [self.view layoutIfNeeded];
    }];
}

@end
```

![Simulator Screen Recording - iPhone 12 Pro - 2021-09-16 at 14.54.56](/Users/liumengyu/Desktop/Simulator Screen Recording - iPhone 12 Pro - 2021-09-16 at 14.54.56.gif)

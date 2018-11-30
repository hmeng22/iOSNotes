## Cumtom View

UIView : drawRect or layoutSubviews
	每一次改变frame时,layoutSubviews都会执行.

首先设置View背景颜色,否则重绘会出现重叠.

    self.backgroundColor = [UIColor clearColor];


layoutSubviews在以下情况下会被调用：
1、init初始化不会触发layoutSubviews。
2、addSubview会触发layoutSubviews。
3、设置view的Frame会触发layoutSubviews，当然前提是frame的值设置前后发生了变化。
4、滚动一个UIScrollView会触发layoutSubviews。
5、旋转Screen会触发父UIView上的layoutSubviews事件。
6、改变一个UIView大小的时候也会触发父UIView上的layoutSubviews事件。
7、直接调用setLayoutSubviews。

drawRect在以下情况下会被调用：
1、如果在UIView初始化时没有设置rect大小，将直接导致drawRect不被自动调用。drawRect 掉用是在Controller->loadView, Controller->viewDidLoad 两方法之后掉用的.所以不用担心在控制器中,这些View的drawRect就开始画了.这样可以在控制器中设置一些值给View(如果这些View draw的时候需要用到某些变量 值).
2、该方法在调用sizeToFit后被调用，所以可以先调用sizeToFit计算出size。然后系统自动调用drawRect:方法。
3、通过设置contentMode属性值为UIViewContentModeRedraw。那么将在每次设置或更改frame的时候自动调用drawRect:。
4、直接调用setNeedsDisplay，或者setNeedsDisplayInRect:触发drawRect:，但是有个前提条件是rect不能为0。
以上1,2推荐；而3,4不提倡

```objectivec
// Only override drawRect: if you perform custom drawing.
- (void)drawRect:(CGRect)rect {}
  [view setNeedsDisplay];
// Overridden by subclasses to layout subviews when layoutIfNeeded is
invoked.
// The default implementation of this method does nothing
- (void)layoutSubviews{}
  [view setNeedsLayout];

Simple Drawing Example 
-(void)drawRect:(CGRect)rect
{ 
//Get the current drawing context
  CGContextRef context = UIGraphicsGetCurrentContext();
  // Drawing lines with a white stroke color
  [[UIColor whiteColor] set];
  // Alternatively: Drawing lines with a white stroke color
  CGContextSetRGBStrokeColor(context, 1.0, 1.0, 1.0, 1.0);
  // Draw a single line from left to right
  CGContextMoveToPoint(context, 10.0, 30.0);
  CGContextAddLineToPoint(context, 310.0, 30.0);
  CGContextStrokePath(context);
}
```

1. Frame 以父视图为起点,得出的位置信息
2. Bounds 以iOS系统坐标原点为起点(0,0)
3. 边框裁剪 clipsToBounds
4. setUserInteractionEnabled

```objectivec
-(CGRect)frame{
    return CGRectMake(self.frame.origin.x,self.frame.origin.y,self.frame.size.width,self.frame.size.height);
}
-(CGRect)bounds{
    return CGRectMake(0,0,self.frame.size.width,self.frame.size.height);
}
很明显，bounds的原点是(0,0)点（就是view本身的坐标系统，默认永远都是0，0点，除非认为setbounds），而frame的原点却是任意的（相对于父视图中的坐标位置）。
```

```objectivec
addSubView:                     // 添加子视图
insertSubview:atIndex:          // 视图插入到指定索引位置
insertSubview:ahoveSubview:     // 视图插入指定视图之上
insertSubview:belowSubview:     // 视图插入指定视图之下
bringSubviewToFront:            // 把视图移动到最顶层
sendSubviewToBack:              // 把视图移动到最底层
exchangeSubViewAtIndex:withSubviewAtIndex://把两个索引对应的视图调换位置
removeFromSuperview:            // 把视图从父视图中移除

alpha // 透明度
backgroundColor // 背景颜色
subViews // 子视图集合
hidden // 是否隐藏
tag // 标签值
superview // 父视图
mulitpleTouchEnaled // 是否开启多点触摸
userInteractionEnabled // 是否响应触摸事件

坐标变换通过transform属性来改变
	CGAffineTransformScale 对视图比例缩放
	CGAffineTransformRotae 对视图做变焦旋转
	CGAffineTransformTranslate 对视图相对原位置做平移
	
	CGAffineTransform transform = rootView.transform;
	rootView.transform = CGAffineTransformScale(transform,0.5,0.5);
	rootView.transform = CGAffineTransformRotae(transform,0.33);
	CGAffineTransformScale(transform,0.5,0.5);
	rootView.transform = CGAffineTransformTranslate(transform,100,100);
```


Events 事件

```objectivec
UIView :
- touchesBegan:withEvent:
- touchesMoved:withEvent:
- touchesEnded:withEvent:
- touchesCancelled:withEvent:

- locationInView
- previousLocationInView

UIControl :
- addTarget:action:forControlEvents:
- sendActionsForControlEvents:UIControlEventValueChanged

- beginTracking
- continueTracking
- endTracking
```

```objectivec
// 设置阴影
myview.layer.shadowOpacity = 0.5;// 阴影透明度
myview.layer.shadowColor = [UIColor grayColor].CGColor;// 阴影的颜色
myview.layer.shadowRadius = 3;// 阴影扩散的范围控制
myview.layer.shadowOffset  = CGSizeMake(1, 1);// 阴影的范围

// 设置圆角
myview.layer.cornerRadius = 8;
myview.layer.masksToBounds = YES;

// 设置边框
myview.layer.borderWidth = 8;
myview.layer.borderColor =[[UIColor grayColor] CGColor];
```

```objectivec
// Duplicate UIView
- (UIView*)duplicate:(UIView*)view
{
    NSData * tempArchive = [NSKeyedArchiver archivedDataWithRootObject:view];
    return [NSKeyedUnarchiver unarchiveObjectWithData:tempArchive];
}
```

```objectivec
//获得View图像
- (UIImage *)imageFromView:(UIView *)theView
{
    UIGraphicsBeginImageContext(theView.frame.size);
    CGContextRef context = UIGraphicsGetCurrentContext();
    [theView.layer renderInContext:context];
    UIImage *theImage = UIGraphicsGetImageFromCurrentImageContext();
    UIGraphicsEndImageContext();

    return theImage;
}

//获得View某个范围内的图像
- (UIImage *)imageFromView:(UIView *)theView atFrame:(CGRect)r
{
    UIGraphicsBeginImageContext(theView.frame.size);
    CGContextRef context = UIGraphicsGetCurrentContext();
    CGContextSaveGState(context);
    UIRectClip(r);
    [theView.layer renderInContext:context];
    UIImage *theImage = UIGraphicsGetImageFromCurrentImageContext();
    UIGraphicsEndImageContext();

    return  theImage;
}
```
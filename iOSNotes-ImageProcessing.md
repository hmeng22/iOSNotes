* 基本格式

原点在左上角

```objectivec
UIGraphicsBeginImageContext(newSize);
CGContextRef context = UIGraphicsGetCurrentContext();

...

UIGraphicsEndImageContext();
```
```objectivec
CGContextRef context = UIGraphicsGetCurrentContext();
CGContextSaveGState(context);

...

CGContextDrawImage(context, inRect, image);
UIImage* newImage = UIGraphicsGetImageFromCurrentImageContext();
CGContextRestoreGState(context);
```

* 裁剪

```objectivec
//定义裁剪的区域相对于原图片的位置
CGRect subImageRect = CGRectMake(START_X, START_Y, WIDTH, HEIGHT);
CGImageRef imageRef = image.CGImage;
CGImageRef subImageRef = CGImageCreateWithImageInRect(imageRef, subImageRect);
UIGraphicsBeginImageContext(CGSizeMake(WIDTH, HEIGHT););
CGContextRef context = UIGraphicsGetCurrentContext();
CGContextDrawImage(context, subImageRect, subImageRef);
UIImage* subImage = [UIImage imageWithCGImage:subImageRef];
UIGraphicsEndImageContext();
```

* 旋转 : 旋转 + 平移

1. 平移context原点到原图的中心位置(width/2, height/2)
2. 对context进行旋转操作。
3. 将旋转后的图像的中心点重新移回原图的中心点(-width/2, -height/2)

```objectivec
UIGraphicsBeginImageContext(newSize);
CGContextRef context = UIGraphicsGetCurrentContext();
    
CGContextTranslateCTM(context, newSize.width/2, newSize.height/2);
CGContextRotateCTM(context, radian);
CGContextTranslateCTM(context, -newSize.width/2, -newSize.height/2);

CGContextDrawImage(context, newSize, image.CGImage);
UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
UIGraphicsEndImageContext();
```

* 拉伸

```objectivec
UIImageResizingModeStretch：拉伸模式，通过拉伸UIEdgeInsets指定的矩形区域来填充图片
UIImageResizingModeTile：平铺模式，通过重复显示UIEdgeInsets指定的矩形区域来填充图片

CGFloat top = 25;		// 顶端盖高度
CGFloat bottom = 25 ;	// 底端盖高度
CGFloat left = 10;		// 左端盖宽度
CGFloat right = 10;		// 右端盖宽度
UIEdgeInsets insets = UIEdgeInsetsMake(top, left, bottom, right);
// 指定为拉伸模式，伸缩后重新赋值
image = [image resizableImageWithCapInsets:insets resizingMode:UIImageResizingModeStretch];
```

* 合并图片

```objectivec
+ (UIImage *)addTwoImageToOne:(UIImage *)oneImg twoImage:(UIImage *)twoImg topleft:(CGPoint)tlPos
{
    UIGraphicsBeginImageContext(oneImg.size);
    
    [oneImg drawInRect:CGRectMake(0, 0, oneImg.size.width, oneImg.size.height)];
    [twoImg drawInRect:CGRectMake(tlPos.x, tlPos.y, twoImg.size.width, twoImg.size.height)];
    
    UIImage *resultImg = UIGraphicsGetImageFromCurrentImageContext();
    
    UIGraphicsEndImageContext();
    
    return resultImg;
}

```



* 函数

```objectivec
[image drawInRect:CGRectMake(0, 0, width, height)];

CGContextXxxXxxxx();

CGContextScaleCTM(context, 1.0, -1.0);反转画布

CTM : Current Transformation Matrix
CGContextScaleCTM(CGContextRef c, CGFloat sx, CGFloat sy)  // 缩放 
CGContextTranslateCTM(CGContextRef c, CGFloat tx, CGFloat ty)  // 平移
CGContextRotateCTM(CGContextRef c, CGFloat angle)  // 旋转
CGContextConcatCTM(CGContextRef c, CGAffineTransform transform)  // 联结

// CGAffineTransformXxxx
CGAffineTransform transform = CGAffineTransformIdentity;
// 变换 A
transform = CGAffineTransformTranslate(transform, 100, 100);
// 变换 B
transform = CGAffineTransformRotate(transform, M_PI_4);
// 变换 C
transform = CGAffineTransformScale(transform, .5, .5);
CGContextConcatCTM(context, transform);

CGContextDrawImage(context, inRect, image);
```

* 连续图片

```objectivec
NSArray *frames = @[
			[UIImage imageNamed:@"step1"],
			[UIImage imageNamed:@"step2"],
			[UIImage imageNamed:@"step3"],
			[UIImage imageNamed:@"step4"],
			[UIImage imageNamed:@"step5"],
			[UIImage imageNamed:@"step6"],
			[UIImage imageNamed:@"step7"],
			[UIImage imageNamed:@"step8"]];

UIImage *img = [UIImage animatedImageWithImages:frames duration:0.8];
```

* UIColor2RGBA

```objectivec
CGFloat red = 0.0, green = 0.0, blue = 0.0, alpha = 0.0;
[color getRed:&red green:&green blue:&blue alpha:&alpha];
cell.cellFillColorR = red;
cell.cellFillColorG = green;
cell.cellFillColorB = blue;
cell.cellFillColorA = alpha;
    
const CGFloat* components = CGColorGetComponents(cellFillColor.CGColor);
self.cell.cellFillColorR = components[0];
self.cell.cellFillColorG = components[1];
self.cell.cellFillColorB = components[2];
self.cell.cellFillColorA = CGColorGetAlpha(cellFillColor.CGColor);
```
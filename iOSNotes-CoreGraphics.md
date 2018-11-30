## Core Graphics
		
	CGImageCreateWithImageInRect 从原图片中取小图
	CGSize subImageSize = CGSizeMake(WIDTH, HEIGHT);
    //定义裁剪的区域相对于原图片的位置
    CGRect subImageRect = CGRectMake(START_X, START_Y, WIDTH, HEIGHT);
    CGImageRef imageRef = superImage.CGImage;
    CGImageRef subImageRef = CGImageCreateWithImageInRect(imageRef, subImageRect);
    UIGraphicsBeginImageContext(subImageSize);
    CGContextRef context = UIGraphicsGetCurrentContext();
    CGContextDrawImage(context, subImageRect, subImageRef);
    UIImage* subImage = [UIImage imageWithCGImage:subImageRef];
    UIGraphicsEndImageContext();
    //返回裁剪的部分图像
    return subImage;


	/*** *** ***/
	UIGraphicsBeginImageContext(newSize);
	UIImage* newImage = UIGraphicsGetImageFromCurrentImageContext();
	UIGraphicsEndImageContext();
	/*** *** ***/

	//实现逐变颜色填充方法
	CGContextClip(context);
	CGColorSpaceRef rgb = CGColorSpaceCreateDeviceRGB();
	CGFloat colors[] =
	{
	   204.0 / 255.0, 224.0 / 255.0, 244.0 / 255.0, 1.00,
	   29.0 / 255.0, 156.0 / 255.0, 215.0 / 255.0, 1.00,
	   0.0 / 255.0,  50.0 / 255.0, 126.0 / 255.0, 1.00,
	};
	CGGradientRef gradient = CGGradientCreateWithColorComponents
	(rgb, colors, NULL, sizeof(colors)/(sizeof(colors[0])*4));
	CGColorSpaceRelease(rgb);
	CGContextDrawLinearGradient(context, gradient,CGPointMake
	(0.0,0.0) ,CGPointMake(0.0,self.frame.size.height),
	kCGGradientDrawsBeforeStartLocation);
	
而假如我们不保存图形状态，每次设置绘图信息都在同一个上下文中，便会覆盖上一次的状态，最后渲染时只是一种状态，线的颜色啊，线宽什么的都只是以你最后设置的为准

	先用CGContextStrokePath来描线,即形状
	后用CGContextFillPath来填充形状内的颜色, 否则path无法显示

```objectivec
- (void)drawRect:(CGRect)rect {
	CGContextRef context = UIGraphicsGetCurrentContext();
	
	// 拷贝一份副本，放入绘图状态栈中，这份副本中的绘制信息为空
	CGContextSaveGState(context);
	
	// 在当前的上下文中设置绘制信息并进行绘制渲染
	CGContextSetLineWidth(context, 3);
	[[UIColor redColor] set];
	CGContextMoveToPoint(context, 0, 0);
	CGContextAddLineToPoint(context, 100, 100);
	CGContextStrokePath(context);
	// 将绘图上下文栈弹栈，得到上面拷贝的副本并设置为当前上下文
	CGContextRestoreGState(context);
}

- (void)drawRect:(CGRect)rect {
	/*NO.1画一条线
     CGContextSetRGBStrokeColor(context, 0.5, 0.5, 0.5, 0.5);//线条颜色
     CGContextMoveToPoint(context, 20, 20);
     CGContextAddLineToPoint(context, 200,20);
     CGContextStrokePath(context);
    */
    
    /*NO.2写文字
    CGContextSetLineWidth(context, 1.0);
    CGContextSetRGBFillColor (context, 0.5, 0.5, 0.5, 0.5);
    UIFont  *font = [UIFont boldSystemFontOfSize:18.0];
    [@"Hua" drawInRect:CGRectMake(20, 40, 280, 300) withFont:font];
    */
    
    /*NO.3画一个正方形图形 没有边框
    CGContextSetRGBFillColor(context, 0, 0.25, 0, 0.5);
    CGContextFillRect(context, CGRectMake(2, 2, 270, 270));
    CGContextStrokePath(context);
    */
    
    /*NO.4画正方形边框
    CGContextSetRGBStrokeColor(context, 0.5, 0.5, 0.5, 0.5);//线条颜色
    CGContextSetLineWidth(context, 2.0);
    CGContextAddRect(context, CGRectMake(2, 2, 270, 270));
    CGContextStrokePath(context);
    */
    
    /*NO.5画方形背景颜色
    CGContextTranslateCTM(context, 0.0f, self.bounds.size.height);
    CGContextScaleCTM(context, 1.0f, -1.0f);
    UIGraphicsPushContext(context);
    CGContextSetLineWidth(context,320);
    CGContextSetRGBStrokeColor(context, 250.0/255, 250.0/255, 210.0/255, 1.0);
    CGContextStrokeRect(context, CGRectMake(0, 0, 320, 460));
    UIGraphicsPopContext();
    */
 
    /*NO.6椭圆
	CGRect aRect= CGRectMake(80, 80, 160, 100);
	CGContextSetRGBStrokeColor(context, 0.6, 0.9, 0, 1.0);
	CGContextSetLineWidth(context, 3.0);
	CGContextAddEllipseInRect(context, aRect); //椭圆
	CGContextDrawPath(context, kCGPathStroke);
    */
    
    /*NO.7弧线
    CGContextBeginPath(context);
    CGContextSetRGBStrokeColor(context, 0, 0, 1, 1);
    CGContextMoveToPoint(context, 100, 100);
    CGContextAddArcToPoint(context, 50, 100, 50, 150, 50);
    CGContextStrokePath(context);
    */
    
    /*NO.8渐变
    CGContextClip(context);
    CGColorSpaceRef rgb = CGColorSpaceCreateDeviceRGB();
    CGFloat colors[] =
    {
        204.0 / 255.0, 224.0 / 255.0, 244.0 / 255.0, 1.00,
        29.0 / 255.0, 156.0 / 255.0, 215.0 / 255.0, 1.00,
        0.0 / 255.0,  50.0 / 255.0, 126.0 / 255.0, 1.00,
    };
    CGGradientRef gradient = CGGradientCreateWithColorComponents
    (rgb, colors, NULL, sizeof(colors)/(sizeof(colors[0])*4));
    CGColorSpaceRelease(rgb);
    CGContextDrawLinearGradient(context, gradient,CGPointMake
                                (0.0,0.0) ,CGPointMake(0.0,self.frame.size.height),
                                kCGGradientDrawsBeforeStartLocation);
     */
     
     /* NO.9四条线画一个正方形
    //画线
    UIColor *aColor = [UIColor colorWithRed:0 green:1.0 blue:0 alpha:0];
    CGContextSetRGBStrokeColor(context, 1.0, 0, 0, 1.0);
    CGContextSetFillColorWithColor(context, aColor.CGColor);
    CGContextSetLineWidth(context, 4.0);
    CGPoint aPoints[5];
    aPoints[0] =CGPointMake(60, 60);
    aPoints[1] =CGPointMake(260, 60);
    aPoints[2] =CGPointMake(260, 300);
    aPoints[3] =CGPointMake(60, 300);
    aPoints[4] =CGPointMake(60, 60);
    CGContextAddLines(context, aPoints, 5);
    CGContextDrawPath(context, kCGPathStroke); //开始画线
    */

	/*绘制虚线
    CGContextSetLineWidth(context, 5.0);
    CGContextSetStrokeColorWithColor(context, [UIColor blueColor].CGColor);
    CGFloat dashArray[] = {2,6,4,2};
    CGContextSetLineDash(context, 3, dashArray, 4);//跳过3个再画虚线，所以刚开始有6-（3-2）=5个虚点
    CGContextMoveToPoint(context, 10, 200);
    CGContextAddQuadCurveToPoint(context, 150, 10, 300, 200);
    CGContextStrokePath(context);
    */
    
    /*CTM
    NSString *path = [[NSBundle mainBundle] pathForResource:@"dog" ofType:@"png"];
    UIImage *img = [UIImage imageWithContentsOfFile:path];
    CGImageRef image = img.CGImage;
     
    CGContextSaveGState(context);
 
    CGAffineTransform myAffine = CGAffineTransformMakeRotation(M_PI);
    myAffine = CGAffineTransformTranslate(myAffine, -img.size.width, -img.size.height);
    CGContextConcatCTM(context, myAffine);
 
    CGContextRotateCTM(context, M_PI);
    CGContextTranslateCTM(context, -img.size.width, -img.size.height);
 
    CGRect touchRect = CGRectMake(0, 0, img.size.width, img.size.height);
    CGContextDrawImage(context, touchRect, image);
    CGContextRestoreGState(context);
	*/
}
```

```objectivec
图片和字符串
[image drawInRect:CGRectMake()];
[string drawInRect:CGRectMake() withFont:font];
```
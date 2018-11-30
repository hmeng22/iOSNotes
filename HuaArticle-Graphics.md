# Core Graphics


```objectivec
#define RADIANS_TO_DEGREES(x) ((x)/M_PI*180.0)
#define DEGREES_TO_RADIANS(x) ((x*M_PI/180.0)
```

## CGAffineTransform
```objectivec
// Make a Affine Transform
CGAffineTransformIdentity
CGAffineTransformMakeRotation(CGFloat angle)
CGAffineTransformMakeScale(CGFloat sx, CGFloat sy)
CGAffineTransformMakeTranslation(CGFloat tx, CGFloat ty)

// Do Affine Transform
CGAffineTransformRotate(CGAffineTransform t, CGFloat angle)
CGAffineTransformScale(CGAffineTransform t, CGFloat sx, CGFloat sy)
CGAffineTransformTranslate(CGAffineTransform t, CGFloat tx, CGFloat ty)
```

## Graphics
```objectivec
// 1. Get Context
	CGContextRef context = UIGraphicsGetCurrentContext();

	CGContextTranslateCTM(context, width, height);    	CGContextScaleCTM(context, 1.0, -1.0);反转画布
	CGContextRotateCTM(context, 30);
	CGContextConcatCTM(context, transform);
	
	CGContextSaveGState(context);
	
// 2. Path
	CGContextSetAlaha
	CGContextSetStrokeColorWithColor
	CGContextSetRGBStrokeColor
	CGContextSetShadowWithColor
	CGContextSetFillColorWithColor 
	CGContextSetRGBFillColor
	
	CGContextMoveToPoint
	
	// 2.1 Path : Line
		CGContextSetLineWidth
		CGContextSetLineJoin [kCGLineJoinMiter, kCGLineJoinRound, kCGLineJoinBevel]
		CGContextSetLineCap [kCGLineCapButt, kCGLineCapRound, kCGLineCapSquare]
		CGContextSetLineDash
		CGContextAddLineToPoint
		CGContextAddLines
	
	// 2.2 Path : Shapes : Rect, Arc, Curve
		CGContextAddRect
		CGContextAddRects
		CGContextFillRect
		CGContextFillRects
		CGContextAddEllipseInRect
		CGContextFillEllipseInRect
		CGContextAddArc
		CGContextAddArcToPoint
		CGContextAddCurveToPoint
		CGContextAddQuadCurveToPoint
	
	// 2.3 Path : Customize Path
		CGMutablePathRef path = CGPathCreateMutable();
		CGPathMoveToPoint(path, nil, 20, 20);
		CGPathAddLineToPoint(path, nil, 50, 50);
		CGPathAddRect
		CGPathAddEllipseInRect
		CGPathAddArc
		CGPathAddCurveToPoint
		CGPathRelease(path);
		CGContextAddPath(context, path);
	
		UIBezierPath *bezierPath = [[UIBezierPath alloc] init];
		[bezierPath moveToPoint:CGPointMake(0, 0)];
		...
		CGContextAddPath(context, bezierPath);

// 3. Stroke Path
	CGContextStrokePath(context);
	CGContextFillPath(context);
	
	CGContextRestoreGState(context);
```

```objectivec
// Stroke Shapes Directly : Line, Rect, Arc
CGContextStrokeLineSegments
CGContextStrokeRect
CGContextStrokeRects
CGContextStrokeRectWithWidth
CGContextFillRect
CGContextFillRects
CGContextFillEllipseInRect

CGContextDrawPath [kCGPathFill表示用非零绕数规则，kCGPathEOFill表示用奇偶规则，kCGPathFillStroke表示填充，kCGPathEOFillStroke表示描线，不是填充]
```







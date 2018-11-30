# UIKit & Core Animation

```objectivec
UIView *v = [UIView alloc] initWithFrame:CGReckMake(0,0,50,50)];
v.backgroundColor = [UIColor redColor];
[self.view addSubview:v];
```

## UIKit
```objectivec
[UIView beginAnimation:@"vAnimation" context:nil];
[UIView setAnimationDuration:1.0];
[UIView setAnimationDelay:1.0];
[UIView setAnimationRepeatAutoreverses:NO];
[UIView setAnimationRepeatCount:MAXFLOAT];
[UIView setAnimationStartDate:(NSDate *)];
[UIView setAnimationDelegate:self];
[UIView setAnimationWillStartSelector:(SEL)];
[UIView setAnimationDidStopSelector:(SEL)];

// set Animated Properties
v.backgroundColor = [UIColor greenColor];
v.frame = CGReckMake(20,20,100,100);
v.alpha = 1.0;

[UIView commitAnimations];
```
```objectivec
// UIViewAnimationOptionRepeat, Autoreverse, ... ...

[UIView animateWithDuration:1.0 animations:^{
	// set Animated Properties
} completion:^(BOOL finish) {
	// callback
}];

[UIView animateKeyframesWithDuration:5.0 delay:0 options:UIViewAnimationOptionCurveLinear animations:^{
    //第二个关键帧（准确的说第一个关键帧是开始位置）:从0秒开始持续50%的时间，也就是5.0*0.5=2.5秒
    [UIView addKeyframeWithRelativeStartTime:0.0 relativeDuration:0.5 animations:^{
        _imageView.center=CGPointMake(80.0, 220.0);
    }];
    //第三个关键帧，从0.5*5.0秒开始，持续5.0*0.25=1.25秒
    [UIView addKeyframeWithRelativeStartTime:0.5 relativeDuration:0.25 animations:^{
        _imageView.center=CGPointMake(45.0, 300.0);
    }];
    //第四个关键帧：从0.75*5.0秒开始，持所需5.0*0.25=1.25秒
    [UIView addKeyframeWithRelativeStartTime:0.75 relativeDuration:0.25 animations:^{
        _imageView.center=CGPointMake(55.0, 400.0);
    }];
    
} completion:^(BOOL finished) {
    NSLog(@"Animation end.");
}];
    
[UIView animateWithDuration:5.0 delay:0 usingSpringWithDamping:0.1 initialSpringVelocity:1.0 options:UIViewAnimationOptionCurveLinear animations:^{

} completion:nil];

[UIView transitionFromView ];
```


## Core Animation

CALayer' properties are animatable. 

	Animatable Properties
		* backgroundColor
		* borderColor & borderWidth
		* bounds & position
		* contents & contentsRect
		* cornerRadius
		* hidden
		* mask & maskToBound
		* opacity
		* transform : translation, scale, rotation
		* shadowXXX : color, offset, opacity, path, radius

```objectivec
* CALayer
layer.contents = (__bridge id)image.CGImage;
layer.contentsGravity = kCAGravityResizeAspect;
layer.contentsScale = [UIScreen mainScreen].scale;

// Sprite Image , One image with many objects
layer.contentsRect = rect;
// Stretch Rect
layer.contentsCenter = rect;
// Mask
layer.mask = maskLayer;
```
```objectivec
* CATransform3D
// Make a 3D Transform
CATransform3DIdentity
CATransform3DMakeTranslation(Gloat tx, CGFloat ty, CGFloat tz)
CATransform3DTranslate()
CATransform3DMakeScale(CGFloat sx, CGFloat sy, CGFloat sz)
CATransform3DScale()
CATransform3DMakeRotation(CGFloat angle, CGFloat x, CGFloat y, CGFloat z)
CATransform3DRotate()



// apply perspective
transform.m34 = - 1.0 / 500.0;
layer.sublayerTransform = transform;

// Image Back
layer.doubleSided = YES;
```

```objectivec
* CAShapeLayer
UIBezierPath *path = [[UIBezierPath alloc] init];
...
CAShapeLayer *shapeLayer = [CAShapeLayer layer];
shapeLayer.strokeColor = [UIColor redColor].CGColor;
shapeLayer.fillColor = [UIColor clearColor].CGColor;
shapeLayer.lineWidth = 5;
shapeLayer.lineJoin = kCALineJoinRound;
shapeLayer.lineCap = kCALineCapRound;
shapeLayer.path = path.CGPath;
[layer addSublayer:shapeLayer];
```
```objectivec
* CATextLayer
CATextLayer *textLayer = [CATextLayer layer];
textLayer.frame = ;
textLayer.foregroundColor = [UIColor blackColor].CGColor;
textLayer.alignmentMode = kCAAlignmentJustified;
textLayer.wrapped = YES;
textLayer.string = @"text";
textLayer.contentsScale = [UIScreen mainScreen].scale;
[layer addSublayer:textLayer];
```
```objectivec
CAGradientLayer *gradientLayer = [CAGradientLayer layer];
gradientLayer.frame = ;
gradientLayer.colors = @[(__bridge id)[UIColor redColor].CGColor, (__bridge id)[UIColor blueColor].CGColor];
gradientLayer.locations = @[@0.0, @0.25, @0.5];
gradientLayer.startPoint = CGPointMake(0, 0);
gradientLayer.endPoint = CGPointMake(1, 1);
[layer addSublayer:gradientLayer];
```
```objectivec
* CAEmitterLayer
CAEmitterLayer *emitter = [CAEmitterLayer layer];
emitter.frame = self.containerView.bounds;
[self.containerView.layer addSublayer:emitter];
emitter.renderMode = kCAEmitterLayerAdditive;
emitter.emitterPosition = CGPointMake(emitter.frame.size.width / 2.0, emitter.frame.size.height / 2.0);
CAEmitterCell *cell = [[CAEmitterCell alloc] init];
cell.contents = (__bridge id)[UIImage imageNamed:@"Spark.png"].CGImage;
cell.birthRate = 150;
cell.lifetime = 5.0;
cell.color = [UIColor colorWithRed:1 green:0.5 blue:0.1 alpha:1.0].CGColor;
cell.alphaSpeed = -0.4;
cell.velocity = 50;
cell.velocityRange = 50;
cell.emissionRange = M_PI * 2.0;
emitter.emitterCells = @[cell];
```
```objectivec
* CAReplicatorLayer
复制 与 延迟
```
```objectivec
* CATransformLayer
* CAScrollLayer
* CAEAGLLayer
```

```objectivec
* CAMediaTiming
animation.duration = 1.0;
animation.repeatCount = 5;
animation.repeatDuration = INFINITY;
animation.autoreverses = YES;
animation.speed = 2.0;
animation.timeOffset = 0.5;
```


| KeyPath        | KeyPath.value                                      |
|:-------------- |:-------------------------------------------------- |
| position       |                                                    |
| bounds         |                                                    |
| cornerRadius   |                                                    |
| transform      | rotation..x.y.z, scale..x.y.z, translation..x.y.z  |
| zPosition      |                                                    |
| opacity        |                                                    |
| backgroundColor|                                                    |
| borderWidth    |                                                    |
| contents       |                                                    |


### CABasicAnimation
```objectivec
CABasicAnimation *animation = [CABasicAnimation animation];
// [CABasicAnimation animationWithKeyPath:keyPath];

animation.duration = timeValue;
animation.fromValue = fromValue;
animation.toValue = toValue;

[v.layer addAnimation:animation forkey:keyValue];
```

### CAKeyframeAnimation
```objectivec
CAKeyframeAnimation *animation = [CAKeyframeAnimation animation];
// [CAKeyframeAnimation animationWithKeyPath:keyPath];

animation.duration = 3.0;

animation.values = [NSArray arrayWithObjects:(id)[UIColor redColor].CGColor,(id)[UIColor greenColor].CGColor,(id)[UIColor blueColor].CGColor,nil];

CGMutablePathRef path = CGPathCreateMutable();
CGPathMoveToPoint(path, nil, 20, 20);
CGPathAddLineToPoint(path, nil, 50, 50);
animation.path = path;
CGPathRelease(path);

// kcAAnimationRotateAuto(AutoReverse), nil
animation.rotationMode = kcAAnimationRotateAuto;

[v.layer addAnimation:animation forKey:keyValue];
```

### CAAnimationGroup
```objectivec
CAAnimationGroup *animationGroup=[CAAnimationGroup animation];

animationGroup = @[basicAnimation, keyframeAnimation];
animationGroup.beginTime = CACurrentMediaTime() + 5;

[v.layer addAnimation:animationGroup forKey:nil];
```

### CATransition
```objectivec
CATransition *transition = [CATransition animation];

transition.duration = 1.0f;

// fade,movein,push,reveal
// cube,oglFlip,suckEffect,rippleEffect
// pageCurl(UnCurl),cameralIrisHollowOpen(Close)
transition.type = @"cube";
// kCATransitionFromRight,Left,Top,Bottom
transition.subtype=kCATransitionFromRight;

// kCAMediaTimingFunctionLinear,EaseIn,EaseOut,EaseInEaseOut
transition.animationTimingFunction:[CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseOut];

[v.layer addAnimation:transition forKey:@"KCTransitionAnimation"];
```


# Transitions
```objectivec
* Transitioning Delegates<UIViewControllerTransitioningDelegate>
* Animation Controllers <UIViewControllerAnimatedTransitioning> and performe the actual animations.
* Interaction Controllers <UIViewControllerInteractiveTransitioning> and control the interactive transitions
* Transitioning Contexts define the transition's metadata <UIViewControllerContextTransitioning>
* Transition Coordinators run other animations in parallel with the transition animations <UIViewControllerTransitionCoordinator>

1. UIKit asks the toViewController for its transitioning delegate.
2. UIKit asks the transitioning delegate for an animation controller via :
	animationControllerForPresentedController:presentingController:sourceController:
3. UIKit constructs the transitioning context.
4. UIKit queries the duration :
	transitionDuration:
5. UIKit invokes animateTransition: to perform the animation.
6. The animation controller calls completeTransition: to complete the animation.
```

```objectivec
|- ViewController : UIViewController <UIViewControllerTransitioningDelegate>
|	|- animationControllerForPresentedController:presentingController:sourceController:
|	|- animationControllerForDismissedController
|	|- interactionControllerForDismissal:
|
|- Transitions : NSObject <UIViewControllerAnimatedTransitioning>
|	|- transitionDuration:
|	|- animateTransition:
|		|- [transitionContext completeTransition:!transitionContext.transitionWasCancelled];

|- UINavigationViewController
|
|- Interactor : UIPercentDrivenInteractiveTransition <UIViewControllerInteractiveTransitioning>
	|- (BOOL)isInInteraction
	|- (BOOL)shouldCompleteTransition
	|- (UIViewController)fromViewController
	|- attachGestureRecognizerToView
		|- fromViewController = vc
		|- addGestureRecognizer
			|- handleGesture:
				|- CGFloat percentage
				|- switch ( gestureRecognizer.state )
					|- [self updateInteractiveTransition:percentage];
					|- [self cancelInteractiveTransition];
					|- [self finishInteractiveTransition];
	
* transitionContext
UIViewController *fromVC = [transitionContext viewControllerForKey:UITransitionContextFromViewControllerKey];
UIViewController *toVC = [transitionContext viewControllerForKey:UITransitionContextToViewControllerKey];
UIView *container = [transitionContext containerView];
    
UIView *fromView = fromVC.view;
UIView *toView = toVC.view;
    
toView.userInteractionEnabled = YES;
```


# UIDynamics
```objectivec
UIDynamicAnimator *animator = [[UIDynamicAnimator alloc] initWithReferenceView:view];

UIGravityBehavior *gravityBeahvior = [[UIGravityBehavior alloc] initWithItems:@[v]];
gravityBeahvior.magnitude = 10;
[animator addBehavior:gravityBeahvior];

UICollisionBehavior *collisionBehavior = [[UICollisionBehavior alloc] initWithItems:@[v]];
collisionBehavior.translatesReferenceBoundsIntoBoundary = YES;
collisionBehavior.collisionDelegate = self;
[animator addBehavior:collisionBehavior];
<UICollisionBehaviorDelegate>
- (void)collisionBehavior:beganContactForItem:withBoundaryIdentifier:atPoint:
- (void)collisionBehavior:endedContactForItem:withBoundaryIdentifier:

UIAttachmentBehavior *attachmentBehavior = [[UIAttachmentBehavior alloc] initWithItem:v attachedToAnchor:view.center];
[attachmentBehavior setAnchorPoint:point];
[animator addBehavior:attachmentBehavior];

UISnapBehavior *snapBehavior = [[UISnapBehavior alloc] initWithItem:v snapToPoint:view.center];
snapBehavior.damping = 0.5;
[animator addBehavior:snapBehavior];

UIPushBehavior *pushBehavior = [[UIPushBehavior alloc] initWithItems:@[v] mode:UIPushBehaviorModeInstantaneous];
pushBehavior.pushDirection = CGVectorMake(-0.5, 0);
[animator addBehavior:pushBehavior];

UIDynamicItemBehavior *dynamicItemBehavior = [[UIDynamicItemBehavior alloc] initWithItems:@[v]];
dynamicItemBehavior.allowsRotation = YES;//允许旋转
dynamicItemBehavior.elasticity = 0.5;//弹性
dynamicItemBehavior.resistance = 0.5;//阻力
dynamicItemBehavior.angularResistance = 0.5;//角阻力
dynamicItemBehavior.friction = 0.5;//摩擦力
dynamicItemBehavior.density = 0.5;//密度
[animator addBehavior:dynamicItemBehavior];

UIDynamicBehavior *behavior = [[UIDynamicBehavior alloc] init];
[behavior addChildBehavior:xxxBehavior];
[behavior addChildBehavior:yyyBehavior];
[behavior addChildBehavior:zzzBehavior];
[animator addBehavior: behavior];
```
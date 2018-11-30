# Core Animation

Core Animation直接作用在CALayer上的,并非UIView.

Example :

[UIView beginAnimations:nil context:nil];//开始动画
[UIView setAnimationDuration:1.0];//动画持续时长
[UIView setAnimationDelegate:self];//设置动画的回调函数，设置后可以使用回调方法
//修改x，此处的x属性为扩展的Category  
self.testView.x +=100;  
[UIView commitAnimations];//提交动画

[UIView animateWithDuration:0.3 animations:^{  
    //代码段...  
}];

支持动画的视图属性
frame
bounds
center
transform
alpha
backgroundColor
contentStretch


```objectivec
CGPoint fromPoint = self.imageView.center;  
 
//路径曲线  
UIBezierPath *movePath = [UIBezierPath bezierPath];  
[movePath moveToPoint:fromPoint];  
CGPoint toPoint = CGPointMake(300, 460);  
[movePath addQuadCurveToPoint:toPoint controlPoint:CGPointMake(300,0)];

//关键帧  
CAKeyframeAnimation *moveAnim = [CAKeyframeAnimation animationWithKeyPath:@"position"];  
moveAnim.path = movePath.CGPath;  
moveAnim.removedOnCompletion = YES;  
 
//旋转变化  
CABasicAnimation *scaleAnim = [CABasicAnimation animationWithKeyPath:@"transform"];  
scaleAnim.fromValue = [NSValue valueWithCATransform3D:CATransform3DIdentity];  
//x，y轴缩小到0.1,Z 轴不变  
scaleAnim.toValue = [NSValue valueWithCATransform3D:CATransform3DMakeScale(0.1, 0.1, 1.0)];  
scaleAnim.removedOnCompletion = YES;  
 
//透明度变化  
CABasicAnimation *opacityAnim = [CABasicAnimation animationWithKeyPath:@"alpha"];  
opacityAnim.fromValue = [NSNumber numberWithFloat:1.0];  
opacityAnim.toValue = [NSNumber numberWithFloat:0.1];  
opacityAnim.removedOnCompletion = YES;  
 
//关键帧，旋转，透明度组合起来执行  
CAAnimationGroup *animGroup = [CAAnimationGroup animation];  
animGroup.animations = [NSArray arrayWithObjects:moveAnim,scaleAnim,opacityAnim, nil];  
animGroup.duration = 1;  
[self.imageView.layer addAnimation:animGroup forKey:nil];  
```


CATransition

```objectivec
CATransition *transition = [CATransition animation];  
transition.duration = 2.0f;  
transition.type = kCATransitionPush;  
transition.subtype = kCATransitionFromTop;  
[self.view exchangeSubviewAtIndex:1 withSubviewAtIndex:0];  
[self.view.layer addAnimation:transition forKey:@"animation"];

transition.type 的类型可以有
淡化、推挤、揭开、覆盖
NSString * const kCATransitionFade;
NSString * const kCATransitionMoveIn;
NSString * const kCATransitionPush;
NSString * const kCATransitionReveal;
这四种，
transition.subtype 
也有四种
NSString * const kCATransitionFromRight;
NSString * const kCATransitionFromLeft;
NSString * const kCATransitionFromTop;
NSString * const kCATransitionFromBottom;
```


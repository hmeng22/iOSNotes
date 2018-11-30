## Dynamics


* UIDynamicItem : 力学物体的状态,有面积有旋转的质点
* UIDynamicBehavior : 指定UIDynamicItem应该如何运动,即定义适用的物理规则
	* UIGravityBehavior
	* UICollisionBehavior
		* collisionBehavior:beganContactForItem:withBoundaryIdentifier:atPoint:
		* collisionBehavior:beganContactForItem:withItem:atPoint:
		* collisionBehavior:endedContactForItem:withBoundaryIdentifier:
		* collisionBehavior:endedContactForItem:withItem: 
	* UIAttachmentBehavior
	* UISnapBehavior
	* UIPushBehavior
	* UIDynamicItemBehavior
* UIDynamicAnimator : UIDynamicBehavior的容器,添加到容器内的行为将发挥作用；
* ReferenceView : 力学参考系

```objectivec
UIDynamicAnimator *animator = [[UIDynamicAnimator alloc] initWithReferenceView:self.view];
UIGravityBehavior *gravityBeahvior = [[UIGravityBehavior alloc] initWithItems:@[sonView]];
[animator addBehavior:gravityBeahvior];

UICollisionBehavior *collisionBehavior = [[UICollisionBehavior alloc] initWithItems:@[sonView]];
collisionBehavior.translatesReferenceBoundsIntoBoundary = YES;
[animator addBehavior:collisionBehavior];
collisionBehavior.collisionDelegate = self;

self.animator = animator;
```
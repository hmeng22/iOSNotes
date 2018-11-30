## Basic

### SKView & SKScene & SKSpriteNode

```objectivec
SKView *skView = (SKView *)self.view;
skView.showsFPS = YES;
skView.showsNodeCount = YES;

CGFloat w = self.bounds.size.width;
CGFloat h = self.bounds.size.height;

SKScene *skScene = [SKScene sceneWithSize:CGSizeMake(w, h)];
skScene.scaleMode = SKSceneScaleModeResizeFill;
// skScene.scaleMode = SKSceneScaleModeAspectFill;
skScene.userInteractionEnabled = NO;

SKSpriteNode *pearlNode = [SKSpriteNode spriteNodeWithImageNamed:@"xxx.png"];
pearlNode.position = CGPointMake(110, -100);
pearlNode.xScale = 0.5;
pearlNode.yScale = 0.5;

SKTransition *t = [SKTransition revealWithDirection:SKTransitionDirectionLeft duration:1];
[skView presentScene:skScene transition:t];
```


### Set rootGroupNode scale

```objectivec
groupNode.position = CGPointMake(self.bounds.size.width * 0.5, self.bounds.size.height * 0.5);
groupNode.xScale = 1.0;
groupNode.yScale = 1.0;
CGRect currentBox = [groupNode calculateAccumulatedFrame];

CGFloat margin = 25.0;
CGRect maximumAllowedBox = CGRectInset(self.bounds, margin, margin);

CGFloat top = CGRectGetMaxY(currentBbox) - groupNode.position.y;
CGFloat bottom = groupNode.position.y - CGRectGetMinY(currentBbox);
CGFloat maxTopAllowed = CGRectGetMaxY(maximumAllowedBbox) - groupNode.position.y;
CGFloat maxBottomAllowed = groupNode.position.y - CGRectGetMinY(maximumAllowedBbox);

CGFloat left = groupNode.position.x - CGRectGetMinX(currentBbox);
CGFloat right = CGRectGetMaxX(currentBbox) - groupNode.position.x;
CGFloat maxLeftAllowed = groupNode.position.x - CGRectGetMinX(maximumAllowedBbox);
CGFloat maxRightAllowed = CGRectGetMaxX(maximumAllowedBbox) - groupNode.position.x;

CGFloat topScale = top > maxTopAllowed ? maxTopAllowed / top : 1;
CGFloat bottomScale = bottom > maxBottomAllowed ? maxBottomAllowed / bottom : 1;
CGFloat leftScale = left > maxLeftAllowed ? maxLeftAllowed / left : 1;
CGFloat rightScale = right > maxRightAllowed ? maxRightAllowed / right : 1;

CGFloat scale = MIN(topScale, MIN(bottomScale, MIN(leftScale, rightScale)));

groupNode.xScale = scale;
groupNode.yScale = scale;
```


### Add Texture Atlas

```objectivec
SKTexture *t1 = [SKTexture textureWithImageNamed:@"t1.png"];
SKTexture *t2 = [SKTexture textureWithImageNamed:@"t2.png"];
```

```objectivec
1. all sprites into *.atlas
2. Enable Texture Atlas Generation
3. 
SKTextureAtlas *ta = [SKTextureAtlas atlasNamed:@"xxx"];
NSArray *textureNames = [ta textureNames];
NSMutableArray *textureArray = [NSMutableArray new];
for (NSString *name in ta) {
	SKTexture *texture = [ta textureNamed:name];
	[textureArray addObject:texture];
}

SKSpriteNode *tn = [SKSpriteNode spriteNodeWithTexture:[textureArray objectAtIndex:0]];
SKAction *tna = [SKAction animateWithTextures:textureArray timePerFrame:0.07];
[tn runAction:tna];
```


### Add Action

```objectivec
SKAction *wait = [SKAction waitForDuration:2.0];
SKAction *runB = [SKAction runBlock:^{
	...
}];
SKAction *seq = [SKAction sequence:@[wait, runB]];
[node runAction:[SKAction repeatActionForever:seq]];

[SKAction removeFromParent];
```


### Add Particle System

```objectivec
NSString *psPath = [[NSBundle mainBundle] pathForResource:@"xxx" ofType:@"sks"];
SKEmitterNode *en = [NSKeyedUnarchiver unarchiveObjectWithFile:psPath];
en.position = CGPointMake(0.0, 0.0);
[scene addChild:en];
```


### Add PhysicsBody

```objectivec
node.physicsBody = [SKPhysicsBody bodyWithRectangleOfSize:CGSizeMake(2, 2)];
node.physicsBody.dynamic = YES;
node.physicsBody.affectedByGravity = YES;
node.physicsBody.allowsRotation = true;
node.physicsBody.mass = 1.0;
```

```objectivec
// Customized Body Shape
https://daz.sg/spritekit/

SKSpriteNode *node = [SKSpriteNode spriteNodeWithImageNamed:@"xxx"];

CGMutablePathRef path = CGPathCreateMutable();
CGFloat offsetX = node.frame.size.width * node.anchorPoint.x;
CGFloat offsetY = node.frame.size.height * node.anchorPoint.y;
...
CGPathCloseSubpath(path);

node.physicsBody = [SKPhysicsBody bodyWithPolygonFromPath:path];
node.physicsBody.dynamic = YES;
node.physicsBody.categoryBitMask = ;
node.physicsBody.contactTestBitMask = ;
node.physicsBody.collisionBitMask = ;
```


### Add Collision Detection

```objectivec
<SKPhysicsContactDelegate>
static const unit8_t bulletCategory = 1;
static const unit8_t enemyCategory = 2;

typedef enum : NSUInteger {
    ExplosionCategory   = (1 << 0),
    MissileCategory     = (1 << 1),
    MonsterCategory     = (1 << 2)
} NodeCategory;

self.physicsWorld.contactDelegate = self;

node.physicsBody = [SKPhysicsBody bodyWithRectangleOfSize:node.size];
node.physicsBody.dynamic = YES;
node.physicsBody.categoryBitMask = enemyCategory;
node.physicsBody.contactTestBitMask = bulletCategory;
node.physicsBody.collisionBitMask = 0;

contact.bodyA & contact.bodyB
if ((contact.bodyA.categoryBitMask & bulletCategory) != 0) {}
```


### Add Gestures

```objectivec
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event {
	for (UITouch *touch in touches) {
		CGPoint startPoint = [touch locationInNode:self];
		CGPoint endPoint = [touch previousLocationInNode:self];
		
		[scene.physicsWorld enumerateBodiesAlongRayStart:startPoint end:endPoint
			usingBlock:^(body, point, normal, stop){
				SKNode *n = body.node;
				
				[self enumerateChildNodesWithName:node.name ...];
		}];
	}
}
```

```objectivec
SKConstraint *c = [SKConstraint positionX:[SKRange range...]];
c.enabled = false;
node.constraints = @[c];
```


### Add Transition

```objectivec
[SKTransition doorsOpenHorizontalWithDuration:1.0];
[SKTransition doorsOpenVerticalWithDuration:1.0];
[SKTransition doorsCloseHorizontalWithDuration:1.0];
[SKTransition doorsCloseVerticalWithDuration:1.0];
[SKTransition flipHorizontalWithDuration:1.0];
[SKTransition flipVerticalWithDuration:1.0];
[SKTransition moveInWithDirection:*Left duration:1.0];
[SKTransition pushWithDirection:*Right duration:1.0];
[SKTransition revealWithDirection:*Down duration:1.0];
[SKTransition crossFadeWithDuration:1.0];
[SKTransition fadeWithColor:[UIColor darkGrayColor] duration:1.0];
[SKTransition fadeWithDuration:1.0];
```


### Add Audio

```objectivec
#import <AVFoundation/AVFoundation.h>
SKAudioNode *an = [SKAudioNode initWithFileNamed::"xxx"];
an.positional = false;
an.autoplayLooped = false;

[an.avAudioNode.engine start];
```


### Add Joint

```objectivec
nodeA, nodeB
SKPhysicsJointPin *j = [SKPhysicsJointPin jointWithBodyA:nodeA.physicsBody bodyB:nodeB.physicsBody archor:CGPointMake(CGRectGetMidX(nodeA.frame), CGRectGetMinY(nodeA.frame))];
scene.physicsWorld.addJoint(j);
```


### Get NSCoding involved

```objectivec
// something like this
- initWithDecoder:(NSCoder *coder) {
	[super initWithDecoder:coder];
	xxx = [coder decodeBoolForKey:@"aBoolValue"];
}

- encoderWithCoder:(NSCoder *coder) {
	[super encodeWithCoder:coder];
	BOOL b = true;
	[coder encodeBool:b forKey:@"aBoolValue"];
}

// save scene
NSKeyedArchiver *ka = [NSKeyedArchiver archivedDataWithRootObject:self];
[[NSUserDefaults standardUserDefaults] setObject:ka forKey:@"currentScene"];
```


### Get GameplayKit involved

```objectivec
// random number
numberGenerator = GKShuffledDistribution ...
[numberGenerator nextInt];
```


### Get SceneKit involved

```objectivec
SCNScene *s = [SCNScene sceneNamed:@"xxx.dae"];
SK3DNode *3dn = [[SK3DNode alloc] initWithViewportSize:CGSizeMake(300, 300)];
3dn.position = CGPointMake(200, 200);
3dn.scnScene = s;
```


### Get CIFilter involved

```objectivec
// Set CIFilter to a scene
// Set CIFilter to a SKEffectNode
CIFilter *f = [CIFilter filterWithName:@"CIGaussianBlur"];
[f setValue:0.0 forKey:@"inputRadius"];
shouldEnableEffects = true;
```


### Get Camera involved

```
A camera’s descendants are always rendered relative to the camera node’s origin and without applying the camera’s scaling or rotation to them. For example, if your game wants to display scores or other data floating above the gameplay, the nodes that render these elements should be descendants of the current camera node.
```



## Code

### Rotation

```objectivec
CGPoint location = [touch locationInNode:self];
CGPoint offset = CGPointMake(location.x - x, location.y - y);
CGFloat angle = atan2(offset.y, offset.x) - M_PI_2;
```

### Move

```objectivec
CGFloat distance = sqrtf(powf(offset.x, 2.0) + powf(offset.y, 2.0));
CGFloat duration = distance / height*0.8;
SKAction *moveTo = [SKAction movetTo:destination duration:duration];
```

### Touch

```objectivec
for (UITouch *touch in touches) {
  CGPoint location = [touch locationInNode:self];
  NSArray *nodes = [self nodesAtPoint:[touch locationInNode:self]];
  for (SKNode *node in nodes) {
     //go through nodes, get the zPosition if you want
     int nodePos = node.zPosition;

     //or check the node against your nodes
     if ([node.name isEqualToString:@"myNode1"]) {
         //...
     }

     if ([node.name isEqualToString:@"myNode2"]) {
         //...
     }
  }
  NSLog(@"Node's name %@ . The location is %f : %f .", touchedNode.name, location.x, location.y);
}

```

### Sound

```objectives
SKAction *soundAction = [SKAction playSoundFileNamed:@"button.mp3" waitForCompletion:NO] forKey:@"sound"];
```

### Add UIScrollView

```objectivec
自定义scrollView继承UIScrollView.
解决UIScrollView会拦截点击事件.重写touches函数,调用SKScene的touches函数.

@property (nonatomic, strong) SKScene *contentScene;

- (instancetype)initWithFrame:(CGRect)frame {
	self = [super initWithFrame:frame];
	if (self) {
		self.transform = CGAffineTransformMakeScale(-1, 1);
	}
	return self;
}

- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
    [super touchesBegan:touches withEvent:event];
    
    [self.contentScene touchesBegan:touches withEvent:event];
}

- (void)touchesEnded:
- (void)touchesMoved:
- (void)touchesCancelled:


GameScene () <UIScrollViewDelegate>

self.scroll.contentSize = CGSizeMake(0, scene.frame.size.height * 2);

- (void)scrollViewDidScroll:(UIScrollView *)scrollView {
    self.scrollNode.position = scrollView.contentOffset;
}
```

### Simulate ScrollView

```objectivec
声明一个SKNode根结点. 移动根结点实现滑动效果.

@property (nonatomic, strong) SKSpriteNode *scrollNode;

- (void)touchesMoved {
	UITouch *touch = [touches anyObject];
	CGPoint location = [touch locationInNode:self];
	CGPoint pl = [touch previousLocationInNode:self];
	    
	CGFloat newX = self.rootNode.position.x + (location.x - pl.x);
	CGFloat newY = self.rootNode.position.y + (location.y - pl.y);
	
	// 过量移动
	newX = MAX()
	newX = MIN()
	newY = MAX()
	newY = MIN()
	
	self.scrollNode.position = CGPointMake(newX, newY);
}

- (void)touchesEnded {
	// 如果过量移动,恢复位置
}
```

### Position children nodes

Children nodes start from the parent's anchorPoint at the position.

### SKCameraNode

```objectivec
SKCameraNode *cam = [SKCameraNode node];
cam.xScale = 1.0;
cam.yScale = 1.0;
cam.position = CGPointZero;
self.camera = cam;
[self addChild:cam];
```

### Pan Gesture

```objectivec
if (recognizer.state == UIGestureRecognizerStateChanged) {

    CGPoint translation = [recognizer translationInView:self.view];
    self.cam.position = CGPointMake(self.cam.position.x - translation.x, self.cam.position.y + translation.y);
    [recognizer setTranslation:CGPointZero inView:self.view];
}

if (recognizer.state == UIGestureRecognizerStateEnded) {

    CGFloat scrollDuration = 0.2;
    CGPoint velocity = [recognizer velocityInView:self.view];
    CGPoint p = CGPointMake(velocity.x * scrollDuration, velocity.y * scrollDuration);
    SKAction *moving = [SKAction moveTo:CGPointMake(self.cam.position.x - p.x, self.cam.position.y + p.y) duration:scrollDuration];
    moving.timingMode = SKActionTimingEaseOut;
    [self.cam runAction:moving];
}
```

### Pinch Gesture

```objectivec
https://forums.developer.apple.com/thread/25363

CGPoint location = [recognizer locationInView:self.view];
CGPoint l = [self convertPointFromView:location];

if (recognizer.state == UIGestureRecognizerStateChanged) {

    float deltaScale = (recognizer.scale - 1.0)*2;
    float convertedScale = recognizer.scale - deltaScale;
    float newScaleX = self.cam.xScale*convertedScale;
    [self.cam setScale:newScaleX];

    CGPoint locationAfterScale = [self convertPointFromView:location];
    CGPoint locationDelta = CGPointMake(l.x - locationAfterScale.x, l.y - locationAfterScale.y);
    CGPoint newLocation = CGPointMake(self.cam.position.x + locationDelta.x, self.cam.position.y + locationDelta.y);
    self.cam.position = newLocation;
    recognizer.scale = 1.0;

} else if (recognizer.state == UIGestureRecognizerStateEnded) {

    if (self.cam.xScale < MIN_ZOOM_SCALE || self.cam.yScale < MIN_ZOOM_SCALE) {
        [self.cam runAction:[SKAction scaleTo:MIN_ZOOM_SCALE duration:0.2]];
    } else if (self.cam.xScale > MAX_ZOOM_SCALE || self.cam.yScale > MAX_ZOOM_SCALE) {
        [self.cam runAction:[SKAction scaleTo:MAX_ZOOM_SCALE duration:0.2]];
    }
}
```

### SoundManager

```objectivec
+(instancetype)sharedInstance {
	static SoundManager *sharedInstance = nil;
	static dispatch_once_t onceToken;
	dispatch_once(&onceToken, ^{
		sharedInstance = [[self alloc] init];
	});
	return sharedInstance;
}

-(SKAction *)soundButton {
	return [SKAction 
}
```

### Action's name

find and control the action using name property.

action's name : [node runAction:action withKey:@"actionKey"];

### Nodes' frame

calculateAccumulatedFrame : a rectangle that includes the entire area that a node and all of its descendants draw into.

# Sprite Kit

## LifeCycle

```objectivec
<SKSceneDelegate>
	- update:forScene
- update:
	- didEvaluateActionsForScene:
- didEvaluateActions:
	- didSimulatePhysicsForScene:
- didSimulatePhysics:
	- didApplyConstraintsForScene:
- didApllyConstraints:
	- didFinishUpdateForScene:
- didFinishUpdate:
```

```objectivec
- didMoveToView:
```

```objectivec
<SKPhysicsContactDelegate>
- didBeginContact:
- didEndContact:
```

SpriteKit

```objective-c
SKView
	|- SKScene
		|- SKNode
  			|- SKSpriteNode
  			|- SKAction
  			|- SKPhysicsBody
```


### SKView

A view that displays SpriteKit content.

```objectivec
SKView
	|- scene
	|- presentScene:
	|- presentScene:transition:
	|
	|- asynchronous
	|- frameInterval
	|- allowsTransparency
	|- ignoresSiblingOrder
	|- shouldCullNonVisibleNodes
	|
	|- paused
	|- showsFPS
	|- showsQuadCount
	|- showsDrawCount
	|- showsNodeCount
	|- showsPhysics
	|- showsFields
	|
	|- convertPoint:fromScene:
	|- convertPoint:toScene:
	|- textureFromNode:crop:
	|- textureFromNode:
```


### SKScene
A scene of content, is the root node in a tree of SKNode which provide content that the scene animates and renders for display.

A scene calculates the contents of a new frame by processing the following actions in order:

1. The scene calls its update: method.
2. The scene executes actions on its children.
3. The scene calls its didEvaluateActions method.
4. The scene executes any physics simulations on physics bodies in the scene.
5. The scene calls its didSimulatePhysics method.
6. The scene applies any constraints associated with nodes in the scene.
7. The scene calls its didApplyConstraints method.
8. The scene calls its didFinishUpdate method.
9. The scene renders all of its nodes and updates the view to display the new contents.

Your game can provide a delegate object to the scene.

```objectivec
SKScene
	|- + sceneWithSize:
	|- initWithSize:
	|
	|- camera
	|- anchorPoint
	|- size
	|- didChangeSize:
	|- scaleMode
	|- backgroundColor
	|
	|- convertPointFromView:
	|- - convertPointToView:
	|
	|- willMoveFromView:
	|- didMoveToView:
	|- view
	|
	|- delegate
	|
	|- update:
	|- didEvaluateActions
	|- didSimulatePhysics
	|- didApplyConstraints
	|- didFinishUpdate
	|
	|- physicsWorld
	|- audioEngine
	|- listener
```


### SKNode

```objectivec
SKNode
	|- SKSpriteNode
	|- SKCameraNode
	|- SKLightNode
	|- SKAudioNode (holds an AVAudioEngine sound graph)
	|- SKVideoNode (Video as Node, AVPlayer)
	|- SKCropNode (Create a mask which is defined as a SKNode on its children)
	|- SKEffectNode (applies CIFilter on the entire scene)
	|- SKEmitterNode
	|- SKFieldNode (physics effects to a portion of the scene)
	|- SKLabelNode
	|- SKShapeNode
	|- SKReferenceNode (references an external serialized node graph)
	|- SK3DNode (render a SceneKit scene as a 2D textured image)
```

```objectivec
SKNode
	|- SKSpriteNode
```

```objectivec
SKConstraint
	|- + positionX:
	|- + positionY:
	|- + positionX:Y:
	|
	|- + orientToNode:offset:
	|- + orientToPoint:offset:
	|- + orientToPoint:inNode:offset:
	|- + zRotation:
	|
	|- + distance:toNode:
	|- + distance:toPoint:
	|- + distance:toPoint:inNode:
	|
	|- referenceNode
	|- enabled
```


### SKAction

```objectivec
SKAction
	|- + moveByX:y:duration:
	|- + moveBy:duration:
	|- + moveTo:duration:
	|- + moveToX:duration:
	|- + moveToY:duration:
	|- + followPath:duration:
	|- + followPath:speed:
	|- + followPath:asOffset:orientToPath:duration:
	|- + followPath:asOffset:orientToPath:speed:
	|
	|- + rotateByAngle:duration:
	|- + rotateToAngle:duration:
	|- + rotateToAngle:duration:shortestUnitArc:
	|
	|- + scaleBy:duration:
	|- + scaleTo:duration:
	|- + scaleXBy:y:duration:
	|- + scaleXTo:y:duration:
	|- + scaleXTo:duration:
	|- + scaleYTo:duration:
	|
	|- + speedBy:duration:
	|- + speedTo:duration:
	|
	|- + unhide
	|- + hide
	|
	|- + fadeInWithDuration:
	|- + fadeOutWithDuration:
	|- + fadeAlphaBy:duration:
	|- + fadeAlphaTo:duration:
	|
	|- + resizeByWidth:height:duration:
	|- + resizeToHeight:duration:
	|- + resizeToWidth:duration:
	|- + resizeToWidth:height:duration:
	|- + setTexture:
	|- + setTexture:resize:
	|- + animateWithTextures:timePerFrame:
	|- + animateWithTextures:timePerFrame:resize:restore:
	|- + setNormalTexture:
	|- + setNormalTexture:resize:
	|- + animateWithNormalTextures:timePerFrame:
	|- + animateWithNormalTextures:timePerFrame:resize:restore:
	|- + colorizeWithColor:colorBlendFactor:duration:
	|- + colorizeWithColorBlendFactor:duration:
	|
	|- + applyForce:duration:
	|- + applyTorque:duration:
	|- + applyForce:atPoint:duration:
	|- + applyImpulse:duration:
	|- + applyAngularImpulse:duration:
	|- + applyImpulse:atPoint:duration:
	|- + changeChargeTo:duration:
	|- + changeChargeBy:duration:
	|- + changeMassTo:duration:
	|- + changeMassBy:duration:
	|- + strengthTo:duration:
	|- + strengthBy:duration:
	|- + falloffTo:duration:
	|- + falloffBy:duration:
	|
	|- + playSoundFileNamed:waitForCompletion:
	|- + play
	|- + pause
	|- + stop
	|- + changePlaybackRateTo:duration:
	|- + changePlaybackRateBy:duration:
	|- + changeVolumeTo:duration:
	|- + changeVolumeBy:duration:
	|- + changeObstructionTo:duration:
	|- + changeObstructionBy:duration:
	|- + changeOcclusionTo:duration:
	|- + changeOcclusionBy:duration:
	|- + changeReverbTo:duration:
	|- + changeReverbBy:duration:
	|- + stereoPanTo:duration:
	|- + stereoPanBy:duration:
	|
	|- + removeFromParent
	|- + runAction:onChildWithName:
	|- + group:
	|- + sequence:
	|- + repeatAction:count:
	|- + repeatActionForever:
	|- + waitForDuration:
	|- + waitForDuration:withRange:
	|- + reachTo:rootNode:duration:
	|- + reachTo:rootNode:velocity:
	|- + reachToNode:rootNode:duration:
	|- + reachToNode:rootNode:velocity:
	|- + actionNamed:
	|- + actionNamed:duration:
	|- + actionNamed:fromURL:
	|- + actionNamed:fromURL:duration:
	|- + customActionWithDuration:actionBlock:
	|- + performSelector:onTarget:
	|- + runBlock:
	|- + runBlock:queue:
	|- - reversedAction
	|
	|- speed
	|- timingMode
	|- timingFunction
	|- duration
```


### SKPhysicsBody

```objectivec
SKPhysicsBody
	|- + bodyWithCircleOfRadius:
	|- + bodyWithCircleOfRadius:center:
	|- + bodyWithRectangleOfSize:
	|- + bodyWithRectangleOfSize:center:
	|- + bodyWithBodies:
	|- + bodyWithPolygonFromPath:
	|- + bodyWithTexture:size:
	|- + bodyWithTexture:alphaThreshold:size:
	|
	|- + bodyWithEdgeLoopFromRect:
	|- + bodyWithEdgeFromPoint:toPoint:
	|- + bodyWithEdgeLoopFromPath:
	|- + bodyWithEdgeChainFromPath:
	|
	|- affectedByGravity
	|- allowsRotation
	|- dynamic
	|- mass
	|- density
	|- area
	|- friction
	|- restitution
	|- linearDamping
	|- angularDamping
	|
	|- categoryBitMask
	|- collisionBitMask
	|usesPreciseCollisionDetection
	|contactTestBitMask
	|- allContactedBodies
	|
	|- applyForce:
	|- applyTorque:
	|- applyForce:atPoint:
	|- applyImpulse:
	|- applyAngularImpulse:
	|- applyImpulse:atPoint:
	|
	|- velocity
	|- angularVelocity
	|- resting
	|
	|- node
	|- joints
	|- fieldBitMask
	|- charge
	|- pinned
```

```objectivec
SKPhysicsJoint
	|- SKPhysicsJointField
	|- SKPhysicsJointLimit
	|- SKPhysicsJointPin
	|- SKPhysicsJointSliding
	|- SKPhysicsJointSpring
```

```objectivec
SKPhysicsContact
	|- bodyA
	|- bodyB
	|- contactPoint
	|- collisionImpulse
	|- contactNormal
```


### SKTransition

```objectivec
SKTransition *reveal = [SKTransition revealWithDirection:SKTransitionDirectionDown duration:1.0];
GameConfigScene *newScene = [[GameConfigScene alloc] initWithSize: CGSizeMake(1024,768)]];
[self.scene.view presentScene: newScene transition: reveal];
```

```objectivec
SKTransition
	|- + crossFadeWithDuration:
	|- + doorsCloseHorizontalWithDuration:
	|- + doorsCloseVerticalWithDuration:
	|- + doorsOpenHorizontalWithDuration:
	|- + doorsOpenVerticalWithDuration:
	|- + doorwayWithDuration:
	|- + fadeWithColor:duration:
	|- + fadeWithDuration:
	|- + flipHorizontalWithDuration:
	|- + flipVerticalWithDuration:
	|- + moveInWithDirection:duration:
	|- + pushWithDirection:duration:
	|- + revealWithDirection:duration:
	|- + transitionWithCIFilter:duration:
	|
	|- pausesIncomingScene
	|- pausesOutgoingScene
```

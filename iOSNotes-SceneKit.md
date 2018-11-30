# Scene Kit

## LifeCycle

1. Updating the scene
2. Applying animations/actions
3. Simulating physics
4. Applying constraints
5. Rendering

```objectivec
<SCNSceneRendererDelegate>
- renderer:updateAtTime:
	actions & animations
- renderer:didApplyAnimationsAtTime:
	physics
- renderer:didSimulatePhysicsAtTime:
	constraints
- renderer:willRenderScene:atTime:
	render
- renderer:didRenderScene:atTime:
```

SceneKit is an Objective-C framework for building apps and games that use 3D graphics, combining a high-performance rendering engine with a high-level, descriptive API. SceneKit supports the import, manipulation, and rendering of 3D assets.

	SCNView & <SCNSceneRenderer>
		|- SCNScene (rootNode)
		|	|- SCNNode (childNodes, parent)
		|		|- SCNCamera (xFov/yFov,zNear/zFar,orthographic)
		|		|- SCNLight (Type,Color,Shadow)
		|		|- SCNGeometry (Materials,Elements)
		|		|	|- SCNMaterial (Diffuse,Multiply,Normal)
		|		|		|- SCNMaterialProperty
		|		|- SCNAction
		|		|- SCNPhysicsBody
		|
		|- SCNTransaction
		|- SCNHitTestResult
	
	<SCNPhysicsContactDelegate>
		|- physicsWorld:didBeginContact:
		|- physicsWorld:didUpdateContact:
		|- physicsWorld:dodEndContact:

### SCNView & SCNSceneRenderer

displays (or renders) SceneKit content and a protocol.

	SCNView
		|- initWithFrame:options:
		|
		|- scene
		|- backgroundColor
		|- allowsCameraControl
		|- antialiasingMode
		|
		|- play:
		|- pause:
		|- stop:
		|
		|- snapshot
		
	<SCNSceneRenderer>
		|- scene
		|
		|- presentScene:withTransition:incomingPointOfView:completionHandler:
		|
		|- pointOfView
		|- showStatistics
		|
		|- overlaySKScene (This can be used to present SKScene)
	 	|
	 	|- ...

### SCNSceneSource & SCNScene

	SCNSceneSource
		|- url
		|- data
		|- propertyForKey:
		|
		|- + sceneSourceWithURL:options:
		|- + sceneSourceWithData:options:
		|- initWithURL:options:
		|- initWithData:options:
		|
		|- sceneWithOptions:statusHandler:
		|- sceneWithOptions:error:
		|
		|- identifiersOfEntriesWithClass:
		|- entryWithIdentifier:withClass:
		|- entriesPassingTest:


a three-dimensional scene.

	SCNScene
		|- scene
		|- sceneNamed:
		|- sceneNamed:inDirectory:options:
		|- sceneWithURL:options:error:
		|- sceneWithMDLAsset:
		|
		|- rootNode
		|- background
		|- paused
		|
		|- attributeForKey:
		|- setAttribute:forKey:
		|
		|- physicsWorld
		|
		|- particleSystems
		|- addParticleSystem:withTransform:
		|- removeParticleSystem:
		|- removeAllParticleSystems


### SCNNode

The basic building block of a scene.

	SCNNode
		|- node
		|- nodeWithGeometry:
		|- nodeWithMDLObject:
		|
		|- name
		|- light
		|- camera
		|- geometry
		|- morpher
		|- skinner
		|- categoryBitMask
		|
		|- presentationNode
		|- paused
		|
		|- transform
		|- position
		|- rotation
		|- eulerAngles
		|- orientation
		|- scale
		|- worldTransform
		|- constriaints
		|
		|- hidden
		|- opacity
		|- renderingOrder
		|- castsShadow
		|
		|- parentNode
		|- childNodes
		|- addChildNode:
		|- insertChildNode:atIndex:
		|- removeFromParentNode
		|- replaceChildNode:with:
		|
		|- childNodesPassingTest:
		|- childNodeWithName:recursively:
		|- enumerateChildNodesUsingBlock:
		|
		|- filters
		|- rendererDelegate
		|
		|- physicsBody
		|- physicsField
		|
		|- particleSystems
		|- addParticleSystem:withTransform:
		|- removeParticleSystem:
		|- removeAllParticleSystems
		|
		|- addAudioPlayer:
		|- audioPlayers
		|- removeAudioPlayer:
		|- removeAllAudioPlayers
		
	<SCNAnimatable>
		|- addAnimation:forKey:
		|- animationForKey:
		|- animationKeys
		|- removeAllAnimations
		|- removeAnimationForKey:
		|- removeAnimationForKey:fadeOutDuration:
		|- pauseAnimationForKey:
		|- resumeAnimationForKey:
		|- isAnimationForKeyPaused:
		
	<SCNActionable>
		|- runAction:
		|- runAction:completionHandler:
		|- runAction:forKey:
		|- runAction:forKey:completionHandler:
		|- actionForKey:
		|- hasActions
		|- actionKeys
		|- removeActionForKey:
		|- removeAllActions


### SCNReferenceNode

A placeholder for content to be loaded from a separate scene file. When you tell a reference node to load its content, SceneKit loads the referenced scene file and makes children of the scene file’s root node become children of the reference node.

	SCNReferenceNode
		|- referenceURL
		|- loadingPolicy
		|
		|- initWithURL:
		|- + referenceNodeWithURL:
		|- load
		|- loaded
		|- unload




### SCNCamera

	SCNCamera
		|- camera
		|- cameraWithMDLCamera:
		|
		|- name
		|
		|- xFov
		|- yFov
		|- zNear
		|- zFar
		|- automaticallyAdjustsZRange
		|
		|- usersOrthographicProjection
		|- orthographicScale
		|- projectionTransform
		|- setProjectionTransform:
		|
		|- focalDistance
		|- focalSize
		|- focalBlurRadius
		|- aperture
		|
		|- categoryBitMask
		
	SCNConstraint
		|- SCNTransformConstraint
		|- SCNIKConstraint
		|- SCNBillboardConstraint
	  	|- SCNLookAtConstraint : is always points toward another node.
	          |- gimbalLockEnabled : whether constrained nodes are allowed to rotate
	          |- target
	          |
	          |- + lookAtConstraintWithTarget:


### SCNPhysicsBody & SCNPhysicsShape

The physics body’s type defines how the body interacts with forces and other bodies in the simulation.

The physics boyd's shape defines the three-dimensional form of the body for collision detection purposes.

	|- Static : unaffected by forces and collisions and cannot move.
	|- Dynamic : affected by forces and collisions with other body types.
	|- Kinematic : not affected by forces or collisions, but by moving them directly you can cause collisions that affect dynamic bodies.
	
	SCNPhysicsBody
		|- physicsShape
		|- type
		|- velocityFactor
		|- angularVelocityFactor
		|- affectedByGravity
		|
		|- mass
		|- charge
		|- friction
		|- rollingFriction
		|- restitution
		|- damping
		|- angularDamping
		|- momentOfInertia
		|- usesDefaultMomentOfInertia
		|
		|- + bodyWithType:shape:
		|- + staticBody
		|- + dynamicBody
		|- + kinematicBody
		|
		|- categoryBitMask
		|- contactTestBitMask
		|- collisionBitMask
		|
		|- applyForce:impulse:
		|- applyForce:atPosition:impulse:
		|- applyTorque:impulse:
		|- clearAllForces
		|
		|- velocity
		|- angularVelocity
		|- isResting
		|- allowsResting
		|- resetTransform
	
	SCNPhysicsShape
		|- sourceObject
		|- options
		|- transforms
		|
		|- + shapeWithGeometry:options:
		|- + shapeWithNode:options:
		|- + shapeWithShapes:transforms:

An SCNPhysicsField object applies forces to objects in an area of effect within a scene.

	SCNPhysicsField
		|
		|- + dragField
		|- + vortexField
		|- + radialGravityField
		|- + linearGravityField
		|- + noiseFieldWithSmoothness:animationSpeed:
		|- + turbulenceFieldWithSmoothness:animationSpeed:
		|- + springField
		|- + electricField
		|- + magneticField
		|- + customFieldWithEvaluationBlock:
		|
		|- halfExtent
		|- scope
		|- usesEllipsoidalExtent
		|- offset
		|- direction
		|- strength
		|- falloffExponent
		|- minimumDistance
		|- active
		|- exclusive
		|
		|- categoryBitMask

SCNPhysicsWorld : read the physicsWorld property of an SCNScene object.

	SCNPhysicsWorld
		|- gravity
		|- speed
		|- timeStep
		|- updateCollisionPairs
		|
		|- allBehaviors
		|- addBehavior:
		|- removeBehavior:
		|- removeAllBehaviors
		|
		|- contactDelegate
		|- contactTestBetweenBody:andBody:options:
		|- contactTestWithBody:options:
		|
		|- rayTestWithSegmentFromPoint:toPoint:options:
		|- convexSweepTestWithShape:fromTransform:toTransform:options:
		
	SCNPhysicsBehavior
		|- SCNPhysicsHingeJoint
		|- SCNPhysicsBallSocketJoint
		|- SCNPhysicsSliderJoint
		|- SCNPhysicsVehicle & SCNPhysicsVehicleWheel




### SCNLight

	SCNLight
		|- light
		|- lightWithMDLLight:
		|
		|- type
		|- color
		|- gobo
		|
		|- name
		|
		|- castsShadow
		|- shadowRadius
		|- shadowColor
		|- shadowMapSize
		|- shadowSampleCount
		|- shadowMode
		|- shadowBias
		|- orthographicScale
		|- zNear
		|- zFar
		|
		|- categoryBitMask
		
	SCNLight
		|- ambient : from all directions with equal effect
		|- directional : from one direction
		|- omnidirectional : all directions from a single point
		|- spotlights : a cone-shaped area

### SCNGeometry & SCNMaterial

	SCNGeometry
		|- geometry
		|- geometryWithSources:elements:
		|- geometryWithMDLMesh:
		|
		|- name
		|- levelsOfDetail
		|
		|- materials
		|- firstMaterial
		|- materialWithName:
		|- insertMaterial:atIndex:
		|- removeMaterialAtIndex:
		|- replaceMaterialAtIndex:withMaterial:
		|
		|- geometryElements
		|- geometrySources
		|- geometryElementCount
		|- geometryElementAtIndex:
		|- geometrySourcesForSemantic:
	
		|- SCNGeometrySource
		|- SCNGeometryElement
	
	    SCNMaterial & SCNMaterialProperty
	        |- material
	        |- materialWithMDLMaterial:
	        |
	        |- diffuse : the material’s diffuse response to lighting
	        |- ambient : the material’s response to ambient lighting
	        |- specular : 
	        |- normal
	        |- reflective : the reflected color for each point on a surface
	        |- emission : the color emitted by each point on a surface
	        |- transparent : the opacity of each point in a material
	        |- multiply : multiplied with pixels in a material after all others
	        |- ambientOcclusion : multiply with the ambient light
	        |- selfIllumination
	        |
	        |- name
	        |- ...
	
	SCNMorpher : ?
		|- 
	
	SCNSkinner : skeletal animation ?
		|- 
	
	SCNProgram <SCNShadable> : GLSL ?
		|-
		
	SCNGeometry
		|- SCNBox
		|- SCNCapsule
		|- SCNCone
		|- SCNCylinder
		|- SCNFloor
		|- SCNPlane
		|- SCNPyramid
		|- SCNShape
		|- SCNSphere
		|- SCNText
		|- SCNTorus
		|- SCNTube
	
	原型(Primitives) : 圆锥体(cone),圆环体(torus),胶囊体(capsule),水管体(tube),锥体(pyramid),立方体(box),球体(sphere),圆柱体(cylinder).

An SCNGeometry object represents a three-dimensional shape—a collection of vertices, normals and texture coordinates that define a surface, also known as a model or mesh.

Geometry defines only the form or shape for a visible object.
Materials specify how a surface is colored or textured and how it responds to lights in the scene.

SCNMaterial : 指定物体表面颜色,材质,对光的敏感度. 一系列组合被称为Mesh.


### SCNAction

* A sequence action has multiple child actions. Each action in the sequence begins after the previous action ends.
* A group action has multiple child actions. All actions stored in the group begin executing at the same time.
* A repeating action stores a single child action. When the child action completes, it is restarted.


	SCNAction
		|- duration
		|- speed
		|- timingMode
		|- timingFunction
		|
		|- + moveByX:y:z:duration:
		|- + moveBy:duration:
		|- + moveTo:duration:
		|- + rotateByX:y:z:duration:
		|- + rotateToX:y:z:duration:
		|- + rotateToX:y:z:duration:shortestUnitArc:
		|- + rotateByAngle:aroundAxis:duration:
		|- + rotateToAxisAngle:duration:
		|- + scaleBy:duration:
		|- + scaleTo:duration:
		|- + fadeInWithDuration:
		|- + fadeOutWithDuration:
		|- + fadeOpacityBy:duration:
		|- + fadeOpacityTo:duration:
		|
		|- + hide
		|- + unhide
		|- + removeFromParentNode
		|
		|- + playAudioSource:waitForCompletion:
		|
		|- + group:
		|- + sequence:
		|- + repeatAction:count:
		|- + repeatActionForever:
		|
		|- + waitForDuration:
		|- + waitForDuration:withRange:
		|
		|- reversedAction
		|
		|- + runBlock:
		|- + runBlock:queue:
		|- + customActionWithDuration:actionBlock:
		|- + javaScriptActionWithScript:duration:


### SCNTransaction

	SCNTransaction
		|- + begin
		|- + commit
		|- + flush
		|
		|- + animationDuration
		|- + setAnimationDuration:
		|- + animationTimingFunction
		|- + setAnimationTimingFunction:
		|
		|- + disableActions
		|- + setDisableActions:
		|
		|- + setCompletionBlock:
		|- + completionBlock
		|
		|- + lock
		|- + unlock
		|
		|- + setValue:forKey:
		|- + valueForKey:



### SCNAudioSource & SCNAudioPlayer



	SCNAudioSource
		|- volume
		|- rate
		|- loops
		|- shouldStream
		|- positional
		|- reverbBlend
		|
		|- + audioSourceNamed:
		|- initWithFileNamed:
		|- initWithURL:
		|
		|- load
	
	SCNAudioPlayer
		|- audioSource
		|- audioNode
		|
		|- initWithSource:
		|- initWithAVAudioNode:
		|- + audioPlayerWithSource:
		|- + audioPlayerWithAVAudioNode:
		|
		|- willStartPlayback
		|- didFinishPlayback

### SCNParticleSystem

Use Xcode.

### SCNHitTestResult

	SCNHitTestResult
		|- node
		|- geometryIndex
		|- faceIndex
		|- localCoordinates
		|- worldCoordinates
		|- localNormal
		|- worldNormal
		|- modelTransform
		|
		|- textureCoordinatesWithMappingChannel:
	
	[SCNView hitTest:options:]
	[SCNNode hitTestWithSegmentFromPoint:toPoint:options:]
	[physicsWorld rayTestWithSegmentFromPoint:toPoint:options:]


### SCNPhysicsContact

SCNPhysicsContact describes a contact between two physics bodies in your scene’s physics simulation. SceneKit automatically creates these objects whenever contacts occur.

	SCNPhysicsContact
		|- nodeA
		|- nodeB
		|- contactPoint
		|- contactNormal
		|- collisionImpulse
		|- penetrationDistance


### SCNRenderer

SCNRenderer renders a SceneKit scene into an arbitrary Metal workflow or OpenGL context.



## Scene Editor

The scene editor allows you to reference content between different .sks(scene) files : reuse the same collection of sprites, edit the original scene and the content automatically updates in every scene.

RIGHT-HANDED Coordinate System.

Cube Map : an array of six images (skybox)


## Basic

```objectivec
* View is SCNView.
* create an assets catalog and change the extension to .scnassets

// An empty scene
SCNScene *scene = [SCNScene scene];

// A camera
SCNNode *cameraNode = [SCNNode node];
cameraNode.camera = [SCNCamera camera];
cameraNode.position = SCNVector3Make(0, 0, 10);
[scene.rootNode addChildNode:cameraNode];

// A spotlight
SCNLight *spotLight = [SCNLight light];
spotLight.type = SCNLightTypeSpot;
spotLight.color = [NSColor redColor];
SCNNode *spotLightNode = [SCNNode node];
spotLightNode.light = spotLight;
spotLightNode.position = SCNVector3Make(0, 1, 0);
[cameraNode addChildNode:spotLightNode];

// Aa ambientlight
SCNLight *ambientLight = [SCNLight light];
ambientLight.type = SCNLightTypeAmbient;
ambientLight.color = [UIColor darkGrayColor];
SCNNode *ambientLightNode = [SCNNode node];
ambientLightNode.light = ambientLight;
[scene.rootNode addChildNode:ambientLightNode];


// A square box
CGFloat side = 15.0;
SCNBox *box = [SCNBox boxWithWidth:side height:side length:side chamferRadius:0];
SCNNode *boxNode = [SCNNode nodeWithGeometry:box];
[scene.rootNode addChildNode:boxNode];
```


### Add Tap Action Handler

```objectivec
UITapGestureRecognizer *tapGesture = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(handleTap:)];
NSMutableArray *gestureRecognizrs = [NSMutableArray array];
[gestureRecognizrs addObject:tapGesture];
[gestureRecognizrs addObjectsFromArray:scnView.gestureRecognizers];
scnView.gestureRecognizers = gestureRecognizrs;

- (void)handleTap:(UITapGestureRecognizer *)gestureRecognize {
	SCNView *scnView = (SCNView *)self.view;
	
	CGPoint p = [gestureRecognize locationInView:scnView];
	NSArray *hitResults = [(SCNView *)self.view hitTest:p options:nil];
	
	if ([hitResults count] > 0) {
		SCNHitTestResult *result = [hitResults objectAtIndx:0];
		SCNVector3 *tapPoint = result.localCoordinates;
		SCNNode *n = result.node;
	}
}
```

```objectivec
// touchesBegin and touchesEnded
check Gestures in touchesEnded:withEvent:
```


### Add Animation

SCNAction objects are very useful for simple and reuseable animations.

```objectivec
SCNAction *moveAction = [SCNAction xxx];

CABasicAnimation *animation = [CABasicAnimation animationWithKeyPath:@"geometry.extrusionDepth"];
animation.fromValue = @0.0;
animation.toValue = @100.0;
animation.duration = 1.0;
animation.autoreverses = YES;
animation.repeatCount = INFINITY;
[node addAnimation:animation forKey:@"extrude"];
```
// Official Example
```objectivec
- (CAAnimation *)loadAnimationFromSceneNamed:(NSString *)sceneName {
    SCNScene *scene = [SCNScene sceneNamed:sceneName];
    
    // find top level animation
    __block CAAnimation *animation = nil;
    [scene.rootNode enumerateChildNodesUsingBlock:^(SCNNode *child, BOOL *stop) {
        if (child.animationKeys.count > 0) {
            animation = [child animationForKey:child.animationKeys[0]];
            *stop = YES;
        }
    }];
    
    return animation;
}

CAAnimation *walkAnimation = [self loadAnimationFromSceneNamed:@"game.scnassets/walk.scn"];
walkAnimation.usesSceneTimeBase = NO;
walkAnimation.fadeInDuration = 0.3;
walkAnimation.fadeOutDuration = 0.3;
walkAnimation.repeatCount = FLT_MAX;
walkAnimation.speed = 1.538;
walkAnimation.animationEvents = @[
	[SCNAnimationEvent animationEventWithKeyTime:0.1 block:^(CAAnimation *animation, id animatedObject, BOOL playingBackward) { NSLog(@"X"); }],
	[SCNAnimationEvent animationEventWithKeyTime:0.6 block:^(CAAnimation *animation, id animatedObject, BOOL playingBackward) { NSLog(@"X"); }]
];

[node addAnimation:_walkAnimation forKey:@"walk"];
[node removeAnimationForKey:@"walk" fadeOutDuration:0.2];
```


### Add Action

```objectivec
[SCNAction playAudioSource:xxx waitForCompletion:NO];
[SCNAction repeatAction:[SCNAction sequence:@[
	[SCNAction fadeOpacityTo:0.01 duration:0.1],
	[SCNAction fadeOpacityTo:1.0 duration:0.1]]]
count:7];
[SCNAction runBlock:^(SCNNode *node) { }];

[node runAction:[SCNAction sequence:@[x,x,x]]];
```


### Add Transaction

```objectivec
[SCNTransaction begin];
[SCNTransaction setAnimationDuration:0.5];
...
xxxNode.xxx = xxx;
...
[SCNTransaction commit];
```

```objectivec
// Official Example
CABasicAnimation *cameraYAnimation = [CABasicAnimation animationWithKeyPath:@"rotation.w"];
cameraYAnimation.fromValue = @(M_PI * 2.0 - _cameraYHandle.rotation.w);
cameraYAnimation.toValue = @(0.0);
cameraYAnimation.additive = YES;
cameraYAnimation.beginTime = CACurrentMediaTime() + 3.0; // wait a little bit before stating
cameraYAnimation.fillMode = kCAFillModeBoth;
cameraYAnimation.duration = 5.0;
cameraYAnimation.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseInEaseOut];
[node addAnimation:cameraYAnimation forKey:nil];

CABasicAnimation *cameraXAnimation = [cameraYAnimation copy];
cameraXAnimation.fromValue = @(-M_PI_2 + _cameraXHandle.rotation.w);
[node addAnimation:cameraXAnimation forKey:nil];
```


### Add PhysicsBody

```objectivec
// Collision bit masks
typedef NS_OPTIONS(NSUInteger, AAPLBitmask) {
    AAPLBitmaskCollision        = 1UL << 2,
    AAPLBitmaskCollectable      = 1UL << 3,
    AAPLBitmaskEnemy            = 1UL << 4,
    AAPLBitmaskSuperCollectable = 1UL << 5,
    AAPLBitmaskWater            = 1UL << 6
};

SCNCapsule *c = [SCNCapsule capsuleWithCapRadius:0.4 height:1];
SCNPhysicsShape *ps = [SCNPhysicsShape shapeWithGeometry:c];

SCNPhysicsBody *pb = [SCNPhysicsBody bodyWithType:SCNPhysicsBodyTypeKinematic shape:ps options:nil]];

node.physicsBody = pb;
node.physicsBody.contactTestBitMask = AAPLBitmaskSuperCollectable | AAPLBitmaskCollectable | AAPLBitmaskCollision | AAPLBitmaskEnemy;
```

```objectivec
[node.physicsBody applyForce:SCNVector3Make(0, 1, 0) atPosition:SCNVector3Make(0, 0, 0) impulse:true];
```

```objectivec
scene.physicsWorld.gravity = SCNVector3Make(2, 0, 0);
```


### Add PhysicsField

```objectivec
SCNPhysicsField *pf = [SCNPhysicsField radialGravityField];
pf.strength = 620;
node.physicsField = pf;
[scene.rootNode addChildNode:node];
```


### Add ParticleSystem

```objectivec
SCNParticleSystem *ps = [SCNParticleSystem particleSystemNamed:@"xxx.scnp" inDirectory:nil];
ps.loops = NO;

// Add ps to a node
SCNMatrix4 psPosition = node.worldTransform;
psPosition.m42 += 0.1;
[scene addParticleSystem:ps withTransform:psPosition];
```

### Add Collision Detection

```objectivec
<SCNPhysicsContactDelegate>
scene.physicsWorld.contactDelegate = self;

contact.nodeA & contact.nodeB
```


### Add Transitions

```objectivec
SCNScene *nextScene = ...;
// Set it to nil first.
scnView.scene = nil;
SKTransition *t = [SKTransition pushWithDirection:SKTransitionDirectionUp duration:1.0];
[scnView presentScene:nextScene withTransition:t incomingPointOfView:nil completionHandler:nil];
```


### Add Audio

```objectivec
SCNAudioSource *as = [SCNAudioSource audioSourceNames:@"xxx.mp3"];
as.loops = YES;
as.volume = 0.5;
as.positional = NO;
as.shouldStream = YES:
[as load];

SCNAudioPlayer *asp = [SCNAudioPlayer audioPlayerWithSource:as];
[node addAudioPlayer:asp];

AVAudioMixerNode *mixer = (AVAudioMixerNode *)asp.audioNode;
mixer.volume = volume based the distance.
```

Load AudioSource Array

```objectivec
typedef NS_ENUM(NSUInteger, AAPLGroundType) {
   AAPLGroundTypeGrass,
   AAPLGroundTypeRock,
   AAPLGroundTypeWater,
   AAPLGroundTypeInTheAir,
   AAPLGroundTypeCount
};

SCNAudioSource *steps[11][5];
for (NSUInteger i = 0; i < 11; i++) {
    steps[i][AAPLGroundTypeGrass] = [SCNAudioSource audioSourceNamed:[NSString stringWithFormat:@"game.scnassets/sounds/Step_grass_0%d.mp3", (uint32_t)i]];
    steps[i][AAPLGroundTypeGrass].volume = 0.5;
    [steps[i][AAPLGroundTypeGrass] load];
}

// Play
NSInteger stepSoundIndex = MIN(11 - 1, (rand() / (float)RAND_MAX) * 11);
[node runAction:[SCNAction playAudioSource:steps[stepSoundIndex][AAPLGroundType] waitForCompletion:NO]];
```


### Load scn Models

```objectivec
SCNScene *scene = [SCNSCene sceneNamed:@"xxx.scnassets/xxx.scn"];
SCNNode *xxxNode = [scene.rootNode childNodeWithName:@"xxx" recursively:YES];
or
SCNNode *xxxNode = [scene.rootNode addChildNode:[scene.rootNode.childNodes[0]]];
```


### Load scn Animations

```objectivec
[node enumerateChildNodesUsingBlock:^(SCNNode *child, BOOL *stop) {
	for (NSString *key in child.animationKeys) {
		CAAnimation *animation = [child animationForKey:key];
		animation.useSceneTimeBase = NO;
		animation.repeatCount = FLT_MAX;
		[child addAnimation:animation forKey:key];
	}
}];
```


### Load dae Models

```objectivec
SCNNode *daeNode = [SCNNode node];
SCNScene *daeScene = [SCNScene sceneNamed:@"xxx.scnassets/xxx.dae"];
NSArray *daeArray = daeScene.rootNode.childNodes;
for (SCNNode *n in daeArray) {
	[daeNode addChildNode:n];
}
[daeScene.rootNode addChildNode:daeNode];
```
```objectivec
NSURL *xxxURL = [[NSBundle mainBundle] URLForResource:@"xxx.scnassets/xxx" withExtension:@"dae"];
SCNSceneSource *sceneSource = [[SCNSceneSource alloc] initWithURL: xxxURL options:nil]];
SCNNode *node = [sceneSource entryWithIdentifier:@"uidInColladaFile" withClass:[SCNNode class]];
[scene.rootNode addChildNode:node];

NSArray *iArray = [sceneSource identifiersOfEntriesWithClass:[SCNNode class]];
NSLog(@"Array : %@", iArray);
// Error : rootNode is special, they cant be un-parented from the scene and moved into new scene. pull them out using the 1st method.
```


### Load dae Animations

```objectivec
NSURL *url = [[NSBundle mainBundle] URLForResource:@"art.scnassets/walking" withExtension:@"dae"];
SCNSceneSource *sceneSource = [SCNSceneSource sceneSourceWithURL:url options:@{SCNSceneSourceAnimationImportPolicyKey:SCNSceneSourceAnimationImportPolicyPlayRepeatedly} ];
NSArray *animationIds = [sceneSource identifiersOfEntriesWithClass:[CAAnimation class]];

int i = 1;
for(NSString *eachId in animationIds){
    CAAnimation *animation = [sceneSource entryWithIdentifier:eachId withClass:[CAAnimation class]];

    NSString *key = [NSString stringWithFormat:@"ANIM_%d", i];
    [node addAnimation:animation forKey:key];
    i++;
}
```


### Load obj Models

```objectivec
NSURL *bananaPath = [[NSBundle mainBundle] URLForResource:@"banana" withExtension:@"obj"];
MDLAsset *bananaAsset = [[MDLAsset alloc] initWithURL:bananaPath];
    
MDLScatteringFunction *scatteringFunction = [[MDLScatteringFunction alloc] init];
MDLMaterial *bananaMaterial = [[MDLMaterial alloc] initWithName:@"material" scatteringFunction:scatteringFunction];
MDLMaterialProperty *p = [[MDLMaterialProperty alloc] initWithName:@"banana.jpg" semantic:MDLMaterialSemanticBaseColor URL:[[NSBundle mainBundle] URLForResource:@"banana" withExtension:@"jpg"]];
[bananaMaterial setProperty:p];
    
MDLMesh *mesh = (MDLMesh *)[bananaAsset objectAtIndex:0];
for (MDLSubmesh *submesh in mesh.submeshes) {
    submesh.material = bananaMaterial;
}
    
SCNView *myView = [SCNView alloc] initWithFrame:self.view.frame];
myView.scene = [SCNScene sceneWithMDLAsset:bananaAsset];
myView.allowsCameraControl = YES;
myView.autoenablesDefaultLighting = YES;
myView.backgroundColor = [UIColor lightGrayColor];
self.view = myview;
```


### Look At SCNNode

```objectivec
SCNLookAtConstraint *constraint = [SCNLookAtConstraint lookAtConstraintWithTarget:xxxNode];
constraint.gimbalLockEnabled = YES;
yyyNode.constraints = @[constraint];
```


### Load A Skybox

```objectivec
aScene.background.contents = @[@"r",@"l",@"t",@"b",@"b",@"f"];
```


### Get Node Size

This is the original size without scale. The real size should consider scale factors.
```objectivec
SCNVector3 vMin = SCNVector3Zero;
SCNVector3 vMax = SCNVector3Zero;
[node getBoundingBoxMin:&vMin max:&vMax];
CGFloat nWidth = vMax.x - vMin.x;
CGFloat nHeight = vMax.y - vMin.y;
CGFloat nDepth = vMax.z - vMin.z;

CGFloat realNWidth = nWidth * node.scale.x;
CGFloat realNHeight = nHeight * node.scale.y;
CGFloat realNDepth = nDepth * node.scale.z;
```


### Change Scenes

```objectivec
scnView.scene = oneScene;
[SCNView presentScene:withTransition:incomingPointOfView:completionHandler:]
```


### Move node

```objectivec
vector_float3 direction;
// deltaTime : time since lastUpdateTime
CGFloat speed = deltaTime * aNumber;
vector_float3 position = SCNVector3ToFloat3(node.position);
node.position = SCNVector3FromFloat3(position + direction * speed);

CGFloat directionAngle = atan2(direction.x, direction.z);
[node runAction:[SCNAction rotateToX:0.0 y:directionAngle z:0.0 duration:0.1 shortestUnitArc:YES]];
```


### Automatic camera animation

```objectivec
NSMapTable<SCNNode *, NSValue *> *cameraPosition;
cameraPosition = [NSMapTable mapTableWithKeyOptions:NSPointerFunctionsOpaqueMemory valueOptions:NSPointerFunctionsStrongMemory];

[cameraPosition setObject:[NSValue valueWithSCNVector3:SCNVector3Make(0, 0, 0)] forKey:[rootNode childNodeWithName:@"xxx" recursively:YES]];

NSValue *possibleValue = [cameraPosition objectForKey:xxx];
if (possibleValue) {
	SCNVector3 position = possibleValue.SCNVector3Value;
}
```


## Get SpriteKit involved 

```objectivec
MainScene (SCNScene) and OverlayScene (SKScene)

a3DScene : SCNScene
a2DScene : SKScene

KVO to transfer information between a3DScene and a2DScene

From SpriteKit to SceneKit :
  In ViewController :
      [a2DScene addObserver:forKeyPath:"key"options:context:];
  In a3DScene :
      - ()observeValueForKeyPath: {
          if ([keyPath isEqualToString:"key"]) {}
      };

From SceneKit to SpriteKit :
  In ViewController :
  	a2DScene.xxx
  	[a2DScene xxx]
```

### Add SpriteKit Scenes as SceneKit Materials

```objectivec
assign an SKScene object to the contents property of an SCNMaterialProperty object.

SCNBox *boxGeo = [SCNBox boxWithWidth:1 height:1 length:1 chamferRadius:0];

SKScene *materialScene = [[SKScene alloc] initWithSize:CGSizeMake(100, 100)];
SKNode *backgroundNode = [[SKSpriteNode alloc] initWithColor:[UIColor blueColor] size:materialScene.size];
backgroundNode.position = CGPointMake(materialScene.size.width/2, materialScene.size.height/2);
SKAction *ba = [SKAction colorizeWithColor:[UIColor blueColor] colorBlendFactor:1 duration:1];
SKAction *ra = [SKAction colorizeWithColor:[UIColor redColor] colorBlendFactor:1 duration:1];
SKAction *ga = [SKAction colorizeWithColor:[UIColor greenColor] colorBlendFactor:1 duration:1];
[backgroundNode runAction:[SKAction repeatActionForever:[SKAction sequence:@[ba, ra, ga]]]];
[materialScene addChild:backgroundNode];

SCNMaterial *m = [SCNMaterial material];
m.diffuse.contents = materialScene;
boxGeo.materials = @[m];

SCNNode *sbNode = [SCNNode nodeWithGeometry:sb];
sbNode.position = SCNVector3Make(0, 0.5, -10);
// need to rotate to update colors
[sbNode runAction:[SCNAction repeatActionForever:[SCNAction rotateByX:0 y:2 z:0 duration:1]]];
[aScene.rootNode addChildNode:sbNode];
```

## Game

1. ​

   ​

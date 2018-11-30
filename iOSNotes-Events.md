# Events

## Gestures

|Gestrue                                        |UIKit class                 |
|:----------------------------------------------|:---------------------------|
|Tapping(any number of taps)                    |UITapGestureRecognizer      |
|Pinching in and out(for zooming a view)        |UIPinchGestureRecognizer    |
|Panning or dragging                            |UIPanGstureRecognizer       |
|Swiping(in any direction)                      |UISwipeGestureRecognizer    |
|Rotating(fingers moving in opposite directions)|UIRotationGestureRecognizer |
|Long press(aka "touch and hold")               |UILondPressGestureRecognizer|

#### Attaching a Gesture Recognizer

1. Create and initialize a gesture recognizer (in ViewController)
`UITapGestureRecognizer *tapRecognizer = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(respondToTapGesture:)];`
2. Configure that gesture
`tapRecognizer.numberOfTapsRequired=1;`
3. Add the tap gesture recognizer to the view
`[self.view addGestureRecognizer:tapRecognizer];`
4. Implement the action method that handles the gesture
`-(void)respondToTapGesture:(UITapGestureRecognizer *)recognizer{...}`

#### Custom Gesture Recognizers

1. Create a subclass of UIGestureRecognizer in Xcode 
2. Add to header:
`#import <UIKit/UIGestureRecognizerSubclass.h>`
3. Add to your implementation file:
`touchesMoved:withEvent:`  
`touchesEnded:withEvent:`
`touchesCancelled:withEvent:`
`touchesBegan:withEvent:`
4. Reset internal state  
`reset`
5. Avoid conflicting gestures 
`canBePreventedByGestureRecognizer:`
`canPreventGestureRecognizer:`

#### Permitting Simultaneous Gesture Recognition
An optional method of the UIGestureRecognizerDelegate protocol.
`gestureRecognizer:shouldRecognizeSimultaneouslyWithGestureRecognizer:`
This method is called when one gesture recognizer’s analysis of a gesture would block another gesture recognizer from recognizing its gesture, or vice versa. This method returns NO by default. Return YES when you want two gesture recognizers to analyze their gestures simultaneously.### UITouch
Each touch is bound to a single finger on the screen, when and where (reduced to a single timestamp and a single point)

* Represents single touch
* Location can be reported for a given view
* Previous location included
* Additional properties:
	* tapCount
	* timestamp
	* phase (began, moved, stationary, ended, cancelled)
* Attached gesture recognizers

```objectivec
// initial touch
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
// updated touch
- (void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event
// cancelled touch (by external event)
- (void)touchesCancelled:(NSSet *)touches withEvent:(UIEvent *)event
// finished touch
- (void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event
```

## Responder Chain
The responder chain is a series of linked responder objects. It starts with the first responder and ends with the application object.

An object becomes the first responder by doing two things:
	1. Overriding the canBecomeFirstResponder method to return YES.	2. Receiving a becomeFirstResponder message. If necessary, an object can send itself this message.

### The First Responder
* Designated object to receive events first
* Explicit : override canBecomeFirstResponder method to return YES or receive a becomeFirstResponder message
initial view -> view -> view controller -> window -> application

myExample 多个view响应同一个手势:

rootView - scrollView - imageView
scrollView会拦截pan手势, 而我希望scrollView响应pan手势的同时加入自己的逻辑代码
imageView用代码生命实现, 所以把pan首饰加入到rootView比较方便
stroyboard添加pan收拾到view上, pan手势control+drag到viewController设置delegate

view实现方法:

```objectivec
-(BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer shouldRecognizeSimultaneouslyWithGestureRecognizer:(UIGestureRecognizer *)otherGestureRecognizer {
    return YES;
}
```
这样, scrollVoew响应pan手势滑动图片的同时, view中的pan动作也会触发

其他, 这个函数会控制当前view是否响应手势

```objectivec
-(BOOL)gestureRecognizerShouldBegin:(UIPanGestureRecognizer *)panGestureRecognizer
{
    return YES;
}
```
感谢 StackOverFlow :
http://stackoverflow.com/questions/7100884/uipangesturerecognizer-only-vertical-or-horizontal
http://stackoverflow.com/questions/14180613/tell-scrollview-to-scroll-after-other-pan-gesture

```objectivec
UIPanGestureRecognizer *panRecognizer = [[UIPanGestureRecognizer alloc] initWithTarget:self action:@selector(move:)];
[panRecognizer setMinimumNumberOfTouches:1];
[panRecognizer setMaximumNumberOfTouches:1];
[panRecognizer setDelegate:self];
[[[scrollView subviews] objectAtIndex:i] addGestureRecognizer:panRecognizer];
```

## Motion

### Orientation
* Tell UIDevice to generate device orientation notifications 
`beginGeneratingDeviceOrientationNotifications`
* Register to receive these notification 
`UIDeviceOrientationDidChangeNotification`
* Turn off device orientation notifications 
`endGeneratingDeviceOrientationNotifications`

```objectivec
[[UIDevice currentDevice] beginGeneratingDeviceOrientationNotifications];
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(orientationChanged:) name:UIDeviceOrientationDidChangeNotification object:nil];

- (void)orientationChanged:(NSNotification *)notification {     // Respond to changes in device orientation}[[NSNotificationCenter defaultCenter] removeObserver:self];[[UIDevice currentDevice] endGeneratingDeviceOrientationNotifications];
```

1.支持旋转

```objectivec	
-(BOOL)shouldAutorotateToInterfaceOrientation:(UIInterfaceOrientation)interfaceOrientation
{
	return YES;
}
```

2.判断方向

```objectivec
-(BOOL)shouldAutorotateToInterfaceOrientation:(UIInterfaceOrientation)interfaceOrientation {
	if(interfaceOrientation==UIInterfaceOrientationLandscapeLeft) {
		//left
	}
	if(interfaceOrientation==UIInterfaceOrientationLandscapeRight) {
		//right
	}
	if(interfaceOrientation==UIInterfaceOrientationPortrait) {
		//up
	}
	if(interfaceOrientation==UIInterfaceOrientationPortraitUpsideDown) {
		//down
	}
	return YES;
}
```
3.会触发的函数

```objectivec
//旋转方向发生改变时
-(void)willAnimateRotationToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration {}

//视图旋转动画前一半发生之前自动调用
-(void)willAnimateFirstHalfOfRotationToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration {}

//视图旋转动画后一半发生之前自动调用
-(void)willAnimateSecondHalfOfRotationFromInterfaceOrientation:(UIInterfaceOrientation)fromInterfaceOrientation duration:(NSTimeInterval)duration {}

//视图旋转之前自动调用
-(void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration {}

//视图旋转完成之后自动调用
-(void)didRotateFromInterfaceOrientation:(UIInterfaceOrientation)fromInterfaceOrientation {}

//视图旋转动画前一半发生之后自动调用
-(void)didAnimateFirstHalfOfRotationToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation {}
```

### Core Motion
	* A CMAccelerometerData object captures the acceleration along each of the spatial axes. 
	* A CMGyroData object captures the rate of rotation around each of the three spatial axes.	* A CMDeviceMotion object encapsulates several different measurements, including attitude and more useful measurements of rotation rate and acceleration.

The CMMotionManager class is the central access point for Core Motion. An app should create only a single instance of the CMMotionManager class.

The CMMotionManager class offers two approaches for obtaining motion data:	
	* Pull. An app requests that updates start and then periodically samples the most recent measurement of motion data.	* Push. An app specifies an update interval and implements a block for handling the data. Then, it requests that updates start, and passes Core Motion an operation queue and the block. Core Motion delivers each update to the block, which executes as a task in the operation queue.

**Pull** is the recommended approach for most apps, especially games. It is generally more efficient and requires less code.  
**Push** is appropriate for data-collection apps and similar apps that cannot miss a single sample measurement. Both approaches have benign thread-safety effects; with push, your block executes on the operation-queue’s thread whereas with pull, Core Motion never interrupts your threads.

	CMAccelerometerData - CMAcceleration
		startAccelerometerUpdates
		startAccelerometerUpdatesToQueue:withHandler:
	CMGyroData - 
		startGyroUpdates
		startGyroUpdatesToQueue:withHandler:
	CMDeviceMotion - 
		startDeviceMotionUpdates
		startDeviceMotionUpdatesToQueue:withHandler:

* CMMotionManager
	* Updating with handler:
		* startXUpdates
		* startXUpdatesToQueue:withHandler
		* Block is added to NSOperationQueue
		* X = [Accelerometer | Gyro | DeviceMotion]
* CMAccelerometerData
* CMGyroData
* CMDeviceMotion
	* Position in 3D Space
		* Attitude:roll,pitch,yaw,rotationMatrix,quaternion
		* x,y,z rotation
	* Acceleration
		* Gravity vector
		* User acceleration vector
* CMMotionActivityManager
* CMMotionActivity
* CMStepCounter


```objectivec
#import <CoreMotion/CoreMotion.h>  
  
CMMotionManager *motionManager = [[CMMotionManager alloc]init]; 
//1. Accelerometer 获取手机加速度数据
CMAccelerometerData *aData = motionManager.accelerometerData; 
double accelerationX = accelData.acceleration.x; 
double accelerationY = accelData.acceleration.y; 
double accelerationZ = accelData.acceleration.z; 

//2. 陀螺仪数据
CMGyroData *gData = motionManager.gyroData;
double gyroX = gData.rotationRate.x; 
double gyroY = gData.rotationRate.y; 
double gyroZ = gData.rotationRate.z; 

//3. Gravity 获取手机的重力值在各个方向上的分量，根据这个就可以获得手机的空间位置，倾斜角度等
CMDeviceMotion *dmData = motionManager.deviceMotion
double gravityX = dmData.gravity.x; 
double gravityY = dmData.gravity.y; 
double gravityZ = dmData.gravity.z; 
 
//获取手机的倾斜角度：
//zTheta是手机与水平面的夹角， xyTheta是手机绕自身旋转的角度
double zTheta = atan2(gravityZ,sqrtf(gravityX*gravityX+gravityY*gravityY))/M_PI*180.0; 
  
double xyTheta = atan2(gravityX,gravityY)/M_PI*180.0; 

//3. DeviceMotion 获取陀螺仪的数据 包括角速度，空间位置等
//旋转角速度：
CMRotationRate rotationRate = motionManager.deviceMotion.rotationRate; 
double rotationX = rotationRate.x; 
double rotationY = rotationRate.y; 
double rotationZ = rotationRate.z; 
 
//空间位置的欧拉角（通过欧拉角可以算得手机两个时刻之间的夹角，比用角速度计算精确地多）
double roll    = motionManager.deviceMotion.attitude.roll; 
double pitch   = motionManager.deviceMotion.attitude.pitch; 
double yaw     = motionManager.deviceMotion.attitude.yaw; 
 
//空间位置的四元数（与欧拉角类似，但解决了万向结死锁问题）
double w = motionManager.deviceMotion.attitude.quaternion.w; 
double wx = motionManager.deviceMotion.attitude.quaternion.x; 
double wy = motionManager.deviceMotion.attitude.quaternion.y; 
double wz = motionManager.deviceMotion.attitude.quaternion.z; 

...
```
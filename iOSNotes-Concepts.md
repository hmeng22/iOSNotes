## Concepts
* **NIB**

	NIB(NeXT Interface Builder) files : previous to iOS 5, XIB file - ‘x’ML, one per screen view.
	Nib files store User Interface

	**File’s Owner object** is a placeholder object that is not created when the nib file is loaded. Instead, you create this object in your code and pass it to the nib-loading code. It is the main link between your application code and the contents of the nib file. it is the controller object that is responsible for the contents of the nib file.

	**The First Responder** is a placeholder object that represents the first object in your application’s dynamically determined responder chain.


* **Bundles**

	A bundle is a folder that contains executable code and resources in a standardized structure.  
	Applications are bundles.  
	NSBundle is used to access bundle resources.
	A bundle is a directory with a standardized hierarchical structure that holds executable code and the resources used by that code.

		NSBundle *mainBundle;
		mainBundle = [NSBundle mainBundle];


* **packages**

	A package is any directory that the Finder presents to the user as if it were a single file.


* **outlets**

	allow graphic controls to be treated as properties.  
	variables that reference views and provide access to their properties.  


* **frameworks**

	A framework is a hierarchical directory that encapsulates shared resources, such as a dynamic shared library, nib files, image files, localized strings, header files, and reference documentation in a single package.

* **autoreleaspool**

	每次事件处理开始时,会自动生成一个autorelesepool,结束的时候释放掉.对于每一个Runloop,系统会隐式创建一个Autorelease pool，这样所有的release pool会构成一个象CallStack一样的一个栈式结构,在每一个Runloop结束时,当前栈顶的Autorelease pool会被销毁,这样这个pool里的每个Object会被release. 

* **System Folders**

	User Home : NSHomeDirectory()  
	Application : [[NSBundle mainBundle] resourcePath]  
	Temporary : NSTemporaryDirectory()  

* **Device**

	[[UIDevice currentDevice] model]];

* **UI frame**

	* frame - size and location in superview’s coordinate system. (looking from outside) Origin:(x, y)
	* bounds - size of the view (and its content origin) in the local coordinate system. (looking from the view itself) Origin:(0.0, 0.0)
	* center - center point of the view in the superview’s coordinate system

Auto Layout : Constraints:priority : Origin at top-left corner

|	iPhone	      |iPhone 4        |iPhone 5        |iPhone 6        |iPhone 6 Plus    |
|:--------------|:---------------|:---------------|:---------------|:----------------|
|Display Size   |3.5 in          |4 in            |4.7 in          |5.5 in           |
|Screen Size    |320 x 480 points|320 x 568 points|375 x 667 points|414 x 736 points |
|Rendered Pixels|640 x 960 (@2x) |640 x 1136 (@2x)|750 x 1334 (@2x)|1242 x 2208 (@3x)|
|Physical Pixels|640 x 960       |640 x 1136      |750 x 1334      |1080 x 1920      |
|Pixels Per Inch|326             |326             |326             |401              |


* **iOS icons and images**
	1. 桌面图标 (app icon)
		* for iPhone6 plus(@3x) : 180 x 180
		* for iPhone 6/5s/5/4s/4(@2x) : 120 x 120
	2. 系统搜索框图标 (Spotlight search results icon)
		* for iPhone6 plus(@3x) : 120 x 120
		* for iPhone6/5s/5/4s/4(@2x) : 80 x 80
	3. 系统设置图标 (Settings icon)
		* for iPhone6 plus(@3x) : 87 x 87
		* for iPhone6/5s/5/4s/4(@2x) : 58 x 58
	4. 启动图片 (launch image)
		* for iPhoen5s/5(@2x) : 640 x 1136
		* for iPhoen4s/4(@2x) : 640 x 960
		* for iPhone 6(@2x) : 750 x 1334
		* for iPhone 6 plus (@3x) : 1242 x 2208

	* Spotlight
		* iOS 5,6  
		base: 29pt, 需要 @1x, @2x, @3x，得出：29 x 29, 58 x 58, 87 x 87
		* iOS 7,8  
		base: 40pt, 需要 @2x, @3x，得出：80 x 80, 120 x 120
	* iPhone App
		* iOS 5,6  
		base: 57pt，需要 @1x, @2x, 得出：57 x 57, 114 x 114
		* iOS 7,8  
		base: 60pt，需要 @2x, @3x，得出：120 x 120, 180 x 180
	* Settings
		* iOS 5,6,7,8  
		base: 29pt，需要 @1x,@2x,@3x，得出：29 x 29, 58x58, 87x87

	* 图片输出列表
		* 180x180
		* 120x120
		* 87x87
		* 80x80
		* 58x58
		* 57x57
		* 29x29
	* 启动图片尺寸输出列表
		* 640x960
		* 640x1136
		* 750x1334
		* 1242x2208



### ARC
Automatic Reference Counting : The runtime keeps track of all memory related operations

* strong reference : denotes an ownership relationship. The reference remains ‘alive’ as long as there is a strong pointer to it.
* weak reference : denotes a weak relationship. The reference does not keep the object ‘alive’. If the object is deallocated the reference becomes nil.
* unsafe_unretained reference : same as a weak reference except that it is not set to nil if the object is deallocated.


### KVC vs. KVO
Key-value Coding (KVC) vs. Observing (KVO)
Coding : Access object properties indirectly through pate : person.supervisor.name
Observing : Notify observers about changes of object properties : person.name = … -> inform observers

KVO Purpose:
Observe and react to changes of your data.

* Automatic UI update (Bindings)
* Synchronization to persistent store (Core Data)
* Consistency checking


## **ViewControllers**

### window
* Every application has at least one window
* Blank canvas to host views

### view
* Draw and animate content
* Layout subviews
* Receive and forward events

### viewControllers
* Manages a self-contained view hierarchy
* Every Screen should have its own controller
* Manages typically one screen
* Flushes the view on low-memory situations
* Resizes the view on orientation change
* Creates modal views on top of the current view

### Life Cycle of Controllers
	
	-(void)init
	-(void)loadView
	-(void)viewDidLoad
		-(void)viewWillLayoutSubviews
		-(void)viewDidLayoutSubviews
	-(void)viewWillAppear:(BOOL)animated
	-(void)viewDidAppear:(BOOL)animated
		-(void)viewWillLayoutSubviews
		-(void)viewDidLayoutSubviews
	-(void)viewWillDisappear:(BOOL)animated
	-(void)viewDidDisappear:(BOOL)animated
	-(void)dealloc
		
### Hidden Status Bar
	- (BOOL)prefersStatusBarHidden
	{
	    return YES;
	}
	

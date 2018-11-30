## Navigation Controller

NavigationController 

	|- viewControllers (NSArray)(Stack)
	|	|- visibleViewController
	|	|- topViewController
	|- UINavigationBar
	|	|- UINavigationItem
	|	|	|- items (NSArray)
	|	|	|- backItem
	|	|	|- topItem
	|	|- delegate
	|- UIToolbar
	|	|- toolbarItems (NSArray)
	|- delegate
	
* has a stack structure to hold the view controllers that where already shown on screen
* push and pop : pushViewController: and popViewController:

```objectivec
- (void)applicationDidFinishLaunching:(UIApplication *)application{
	UIViewController *myViewController = [[MyViewController alloc] init];
	navigationController = [[UINavigationController alloc] initWithRootViewController:myViewController];
	window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
	window.rootViewController = navigationController;
	[window makeKeyAndVisible];}
```
UINavigationController

	pushViewController: animated:
	popViewControllerAnimated: 
	setViewControllers: animated: 
	popToRootViewControllerAnimated:
	popToViewController: animated:
	
	setNavigationBarHidden:animated: 
	setToolbarHidden:animated:
	
UINavigationBar

	backBarButtonItem,leftBarButtonItem
	titleView
	rightBarButtonItem
	
	setToolbarItems:animated: 
	
Tips:	

	Using IB : If you are using Xib Interface then you should assign the NibName before pushing it on the stack.
		DetailViewController *dvc = [[DetailViewController alloc] initWithNibName:@"DetailViewController" bundle:nil];
	
	Using StoryBoard : Make sure you have defined the Identifier as "DetailViewController" for your viewController in attributes Inspector.
		DetailViewController *dvc = [self.storyboard instantiateViewControllerWithIdentifier:@"DetailViewController"];
	
	传统方法 performSegueWithIdentifier

```objectivec
NSMutableDictionary *attr = [NSMutableDictionary new];
attr[UITextAttributeTextColor] = [UIColor colorWithRed:255.0/255.0 green:255.0/255.0 blue:255.0/255.0 alpha:0.6];
attr[UITextAttributeTextShadowColor] = [UIColor colorWithRed:0.0 green:0.0 blue:0.0 alpha:0.8];
attr[UITextAttributeTextShadowOffset] = [NSValue valueWithUIOffset:UIOffsetMake(0, -1)];
attr[UITextAttributeFont] = [UIFont systemFontOfSize:18.0];
[[UINavigationBar appearance] setTitleTextAttributes:attr];
```

Push Into A Black View Controller :

```objectivec
UIStoryboard *storyboard = [UIStoryboard storyboardWithName:@"Main" bundle:nil];
SettingViewContoller *vc = [storyboard instantiateViewControllerWithIdentifier:@"idntifier"];
[self.navigationController pushViewController:vc animated:YES];
```


	
## Tab Bar Controller

TabBarController

	|- viewControllers (NSArray)
	|	|- TabBarItem
	|		|- badgeValue
	|- <UITabBarControllerDelegate>
		
```objectivec
- (void)applicationDidFinishLaunching:(UIApplication *)application {
	tabBarController = [[UITabBarController alloc] init];
	MyViewController* vc1 = [[MyViewController alloc] init];
	MyOtherViewController* vc2 = [[MyOtherViewController alloc] init];
	NSArray* controllers = [NSArray arrayWithObjects:vc1, vc2, nil];
	tabBarController.viewControllers = controllers;
	window.rootViewController = tabBarController;}
```	
UITabBarController

	setViewControllers:animated:
	
	Tab bar controllers support a portrait orientation by default and do not rotate to a landscape orientation unless all of the contained view controllers support such an orientation.
	
	
	
## Page View Controller
	
PageViewController
	
	|- dataSource <UIPageViewControllerDataSource>
	|- delegate <UIPageViewControllerDelegate>
	|- viewControllers (NSArray)
	|- gestureRecognizers (NSArray)
	
```objectivec
NSDictionary * options = [NSDictionary dictionaryWithObject:[NSNumber numberWithInt:UIPageViewControllerSpineLocationMid] forKey:UIPageViewControllerOptionSpineLocationKey];UIPageViewController *pageViewController = [[UIPageViewController alloc] initWithTransitionStyle:UIPageViewControllerTransitionStylePageCurl navigationOrientation:UIPageViewControllerNavigationOrientationHorizontal options:options];
```	
	
UIPageViewController

	initWithTransitionStyle:navigationOrientation:options: 
	setViewControllers:direction:animated:completion:
	
	pageViewController:viewControllerBeforeViewController:
	pageViewController:viewControllerAfterViewController:
	
	
## Split View Controller

SplitViewController

```objectivec
- (void)applicationDidFinishLaunching:(UIApplication *)application {
	MyFirstViewController* firstVC = [[MyFirstViewController alloc] init];
	MySecondViewController* secondVC = [[MySecondViewController alloc] init];
	UISplitViewController* splitVC = [[UISplitViewController alloc] init];
	splitVC.viewControllers = [NSArray arrayWithObjects:firstVC, secondVC, nil];
	window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
	window.rootViewController = splitVC;
	[window makeKeyAndVisible];
	return YES;
}
```


## Popover Controller

	contentSizeForViewInPopover, popoverContentSize

	presentPopoverFromBarButtonItem:permittedArrowDirections:animated:
	presentPopoverFromRect:inView:permittedArrowDirections:animated:
	
	dismissPopoverAnimated: 
	
```objectivec
- (IBAction)toolbarItemTapped:(id)sender{
	MyViewController* content = [[MyViewController alloc] init];
	UIPopoverController* aPopover = [[UIPopoverController alloc] initWithContentViewController:content];
	aPopover.delegate = self;
	// Store the popover in a custom property for later use.
	self.popoverController = aPopover;
	[self.popoverController presentPopoverFromBarButtonItem:sender permittedArrowDirections:UIPopoverArrowDirectionAny animated:YES];}
```
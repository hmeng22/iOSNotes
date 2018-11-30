# Segue

iPhone : push, modal, custom
iPad : push, modal, popover, replace, custom

Modal : 最常用的场景,新的场景完全盖住了旧的那个.用户无法再与上一个场景交互,除非他们先关闭这个场景

Push : 需要Navigation Controller的,不从按钮建立segue,而是从viewController建立,通过判断identifier来执行对应segue

Popover : 浮动窗口
Replace : 替换
Custom : 自定义

	* “Segueway” smooth transition
	* holds an identifier (should be unique) and two view controllers : a source and a destination

* Replace - Replaces the right-hand side of a UISplitViewController (iPad only)
* Popover - Puts the view controller on the screen in a popover (iPad only)
* Modal - Puts the view controller up in a way that blocks the app until it is dismissed 
* Custom - You can create your own subclasses of UIStoryboardSegue

You can programmatically invoke segues using this method in UIViewController:

	-(void)performSegueWithIdentifier:(NSString *)segueId sender:(id)sender;

The segue offers the source VC the opportunity to “prepare” the new VC to come on screen. This method is sent to the VC that contains the button that initiated the segue:

	-(void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
		if ([segue.identifier isEqualToString:@“DoSomething”]) {
			if ([segue.destinationViewController isKindOfClass:[DoSomethingVC class]]) {
				DoSomethingVC *doVC = (DoSomethingVC *)segue.destinationViewController;
				doVC.neededInfo = …;
			}
		}
	}

UIStoryboardSegue:  
every time a segue is triggered, the storyboard calls the method 
`-prepareForSegue:sender:`  
implemented in the current view controller enabling it to transfer all the needed information to the receiver. It is not explicitly created by the programmer, but can be explicitly invoked  
`-performSegueWithIndentifier:sender:`

方法: 
1. AViewController 创建segue, 并命名identifier
2. AViewController 创建-(IBAction)functionName:(UIStoryboardSegue *)segue方法, 此unwind方法会在返回后调用
3. BViewController 添加segue到Exit上, 链接unwind方法并给unwind segue命名
4. Call `-performSegueWithIndentifier:sender:` to perfom unwind
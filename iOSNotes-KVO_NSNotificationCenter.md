## KVO

	[aObject addObserver:self forKeyPath:@"aVariable" options:NSKeyValueObservingOptionOld|NSKeyValueObservingOptionNew context:nil];

	[aObject removeObserver:self forKeyPath:@"aVariable" context:nil];

	-(void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary *)change context:(void *)context
	{
		if ([keyPath isEqual:@"aVariable"]) {
			NSLog(@"aVariable is Changed. %@ . %@ . ", [change objectForKey:@"new"], [change objectForKey:@"old"]);
		}
	}






## NotificationCenter

	// get a reference to the default notification center
	NSNotificationCenter *notificationCenter = [NSNotificationCenter defaultCenter];

	注册接收消息通知
	[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(getNotifiyAction:) name:@"NC_aVariableChanged" object:nil];
	
	-(void)getUserProfileSuccess:(NSNotification*)aNotification
	{
		
	}
	
	[[NSNotificationCenter defaultCenter] removeObserver:self];
	[[NSNotificationCenter defaultCenter] removeObserver:self name:@"NC_aVariableChanged" object:nil];
	
	发送消息
	[[NSNotificationCenter defaultCenter] postNotificationName:@"NC_aVariableChanged" object:self userInfo:nil];
	
When to use ?

* System-wide(UI) events
* Inter-system communication
* Key-value observing
* Some frameworks use notifications

When not to use ?

* To delegate behavior
* High-performance situations
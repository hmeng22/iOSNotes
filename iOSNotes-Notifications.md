# Notifications

	application:didReceiveRemoteNotification:
	application:didReceiveLocalNotification:
	
	registerUserNotificationSettings:
	
	
## Remote Notifications

aka push notifications.

An app must register with Apple Push Notification service(APNs) to receive remote notification sent by app's push provider.

```objectivec
- (void)applicationDidFinishLaunching:(UIApplication *)app {
	UIUserNotificationType types = UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert;
	UIUserNotificationSettings *mySettings = [UIUserNotificationSettings settingsForTypes:types categories:nil];
	[[UIApplication sharedApplication] registerUserNotificationSettings:mySettings];
	[app.registerForRemoteNotifications];
}

// Delegation methods
- (void)application:(UIApplication *)app didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)devToken {
	const void *devTokenBytes = [devToken bytes];
	self.registered = YES;
	[self sendProviderDeviceToken:devTokenBytes]; // custom method}- (void)application:(UIApplication *)app didFailToRegisterForRemoteNotificationsWithError:(NSError *)err {
	NSLog(@"Error in registration. Error: %@", err);
}
```

## Local Notifications

```objectivec
UILocalNotification *localNotif = [[UILocalNotification alloc] init];
localNotif.fireDate = ;
localNotif.timeZone = [NSTimeZone defaultTimeZone];

localNotif.alertBody = NSLocalizedString(@"local", nil);
localNotif.alertAction = NSLocalizedString(@"View Details", nil);
localNotif.alerttitle = NSLocalizedString(@"Item Due", nil);

localNotif.soundName = UILocalNotificationDefaultSoundName;localNotif.applicationIconBadgeNumber = 1;

[[UIApplication sharedApplication] scheduleLocalNotification:localNotif];
```

	presentLocalNotificationNow:
	cancelLocalNotification:
	cancelAllLocalNotifications

```objectivec
UIApplication* app = [UIApplication sharedApplication];NSArray* oldNotifications = [app scheduledLocalNotifications];if ([oldNotifications count] > 0)
	[app cancelAllLocalNotifications];
```

```objectivec
// Handling a local notification when an app is launched
- (BOOL)application:(UIApplication *)app didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
	UILocalNotification *localNotif = [launchOptions objectForKey:UIApplicationLaunchOptionsLocalNotificationKey];
	// UIApplicationLaunchOptionsRemoteNotificationKey
	if (localNotif) {
		NSString *itemName = [localNotif.userInfo objectForKey:ToDoItemKey];
		[viewController displayItem:itemName];  // custom method
		app.applicationIconBadgeNumber = localNotif.applicationIconBadgeNumber-1;=
	}
	[window addSubview:viewController.view];
	[window makeKeyAndVisible];
	return YES;}
```
```objectivec
// Handling a local notification when an app is already running
- (void)application:(UIApplication *)app didReceiveLocalNotification:(UILocalNotification *)notif {
	NSString *itemName = [notif.userInfo objectForKey:ToDoItemKey];
	[viewController displayItem:itemName];  // custom method
	app.applicationIconBadgeNumber = notification.applicationIconBadgeNumber - 1;}
```
# UIUserNotificationAction

```objectivec
// create actions
UIMutableUserNotificationAction *acceptAction = [[UIMutableUserNotificationAction alloc] init];
acceptAction.identifier = @"ACCEPT_IDENTIFIER";
acceptAction.title = @"Accept";
acceptAction.activationMode = UIUserNotificationActivationModeBackground;
acceptAction.destructive = NO;
acceptAction.authenticationRequired = NO;   // If YES requires passcode, but does not unlock the device

UIMutableUserNotificationAction *declineAction = [[UIMutableUserNotificationAction alloc] init];
declineAction.identifier = @"DECLINE_IDENTIFIER";
declineAction.title = @"Decline";
acceptAction.activationMode = UIUserNotificationActivationModeBackground;
declineAction.destructive = YES;
acceptAction.authenticationRequired = NO;

// create a category
UIMutableUserNotificationCategory *inviteCategory = [[UIMutableUserNotificationCategory alloc] init];
inviteCategory.identifier = kInviteCategoryIdentifier;
[inviteCategory setActions:@[acceptAction, declineAction]
                forContext:UIUserNotificationActionContextDefault];
[inviteCategory setActions:@[acceptAction, declineAction]
                forContext:UIUserNotificationActionContextMinimal];
                
// registration
NSSet *categories = [NSSet setWithObjects:inviteCategory, nil];
UIUserNotificationType types = UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert;
UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:types categories:categories];
[[UIApplication sharedApplication] registerUserNotificationSettings:settings];
```

# UILocalNotification

```objectivec
UILocalNotification *notification = [[UILocalNotification alloc] init];
notification.fireDate = [[NSDate date] dateByAddingTimeInterval:15.0];
notification.alertBody = @"This is Local Notification from iOS8-Sampler";
notification.soundName = UILocalNotificationDefaultSoundName;
notification.category = kInviteCategoryIdentifier;
[[UIApplication sharedApplication] scheduleLocalNotification:notification];
```
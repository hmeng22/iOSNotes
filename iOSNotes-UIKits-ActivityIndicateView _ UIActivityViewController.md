## UIActivityIndicatorView

```objectivec
UIActivityIndicatorView *spinner = [[UIActivityIndicatorView alloc] initWithActivityIndicatorStyle:UIActivityIndicatorViewStyleWhiteLarge];
[spinner setColor:[UIColor redColor]];
[spinner setCenter:self.mCollectionView.center];
[self.view addSubview:spinner];
[spinner startAnimating];
[spinner stopAnimating];
```

## UIActivityViewController

```objectivec
NSArray *activities = @[UIActivityTypePostToFacebook,
                            UIActivityTypePostToTwitter,
                            UIActivityTypePostToWeibo,
                            UIActivityTypeMail,
                            UIActivityTypeMessage,
                            UIActivityTypePrint,
                            UIActivityTypeSaveToCameraRoll,
                            UIActivityTypeCopyToPasteboard,
                            UIActivityTypeAssignToContact];

UIActivityViewController *activityCtr = [[UIActivityViewController alloc] initWithActivityItems:@[image] applicationActivities:nil];
NSMutableArray *excludedActivities = activities.mutableCopy;
[excludedActivities addObject:UIActivityTypeAddToReadingList];
[excludedActivities addObject:UIActivityTypePostToFlickr];
[excludedActivities addObject:UIActivityTypePostToTencentWeibo];
[excludedActivities addObject:UIActivityTypePostToVimeo];
[activityCtr setExcludedActivityTypes:excludedActivities];

[self presentViewController:activityCtr animated:YES completion:nil];
```
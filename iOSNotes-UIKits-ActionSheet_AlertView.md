# UIAlertController

```objectivec
UIAlertControllerStyle style = UIAlertControllerStyleAlert;
UIAlertControllerStyle style = UIAlertControllerStyleActionSheet;

UIAlertController *alert = [UIAlertController alertControllerWithTitle:@"TITLE" message:@"MESSAGE" preferredStyle:style];

UIAlertAction *okAction = [UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:^(UIAlertAction *action) { 
	NSLog(@"OK!");
	}];
UIAlertAction *cancelAction = [UIAlertAction actionWithTitle:@"CANCEL" style:UIAlertActionStyleCancel handler:^(UIAlertAction *action) {
	NSLog(@"CANCEL!");
	}];
UIAlertAction *destructiveAction = [UIAlertAction actionWithTitle:@"DESTRUCT" style:UIAlertActionStyleDestructive handler:^(UIAlertAction *action) {
	NSLog(@"DESTRUCT");
	}];

[alert addAction:okAction];
[alert addAction:cancelAction];
[alert addAction:destructiveAction];

[self presentViewController:alert animated:YES completion:^{ }];
```



ActionSheet

	<UIActionSheetDelegate>
	
	-(void)actionSheet:(UIActionSheet *)actionSheet clickedButtonAtIndex:(NSInteger)buttonIndex
	
	-(void)actionSheetCancel:(UIActionSheet *)actionSheet
	-(void)actionSheet:(UIActionSheet *)actionSheet didDismissWithButtonIndex:(NSInteger)buttonIndex
	-(void)actionSheet:(UIActionSheet *)actionSheet willDismissWithButtonIndex:(NSInteger)buttonIndex

```objectivec
UIActionSheet *actionSheet = [[UIActionSheet alloc]  
                                  initWithTitle:@"Menu"  
                                  delegate:self  
                                  cancelButtonTitle:@"cancel"  
                                  destructiveButtonTitle:@"确定"  
                                  otherButtonTitles:@"Undo", @"Reset",nil];  
    actionSheet.actionSheetStyle = UIActionSheetStyleBlackOpaque;  
    [actionSheet showInView:self.view];  
```


AlertView

	<UIAlertViewDelegate>
	
	- (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex
	
```objectivec
UIAlertView *alertView = [[UIAlertView alloc]  
                          initWithTitle:@"AlertView"  
                          message:@"Alert text goes here"  
                          delegate:self  
                          cancelButtonTitle:@"No"  
                          otherButtonTitles:@"Yes", @"Cancel", nil nil];  
[alertView show];  
```
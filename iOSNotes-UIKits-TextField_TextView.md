## UITextField

* 限制输入长度

```objectivec
<UITextFieldDelegate>
self.textField.delegate = self;
- (BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string;
```

* 键盘遮挡输入框

```objectivec
<UITextFieldDelegate>
self.textField.delegate = self;
-(void)textFieldDidBeginEditing:(UITextField *)textField{
	CGRect frame = textField.frame;
	//在这里我多加了62，（加上了输入中文选择文字的view高度）这个依据自己需求而定
	int offset = (frame.origin.y+62)-(self.view.frame.size.height-216.0);//键盘高度216
	[UIView beginAnimations:@"ResizeForKeyboard" context:nil];
	[UIView setAnimationDuration:0.30f];//动画持续时间
	if (offset>0) {
	//将视图的Y坐标向上移动offset个单位，以使下面腾出地方用于软键盘的显示
		self.view.frame = CGRectMake(0.0f, -offset, self.view.frame.size.width, self.view.frame.size.height);
		[UIView commitAnimations];
	}
}
	
-(void)textFieldDidEndEditing:(UITextField *)textField{
	//输入框编辑完成以后，当键盘即将消失时，将视图恢复到原始状态
	self.view.frame = CGRectMake(0, 0, 			self.view.frame.size.width, self.view.frame.size.height);
}
	
-(BOOL)textFieldShouldReturn:(UITextField *)textField{
	[textField resignFirstResponder];
	return YES;
}
```

## TextView

* 显示最下面一行文字

```objectivec
[UIView setAnimationsEnabled:NO];
[_textView scrollRangeToVisible:NSMakeRange([_textView.text length] - 1,0)];
[UIView setAnimationsEnabled:YES];
```
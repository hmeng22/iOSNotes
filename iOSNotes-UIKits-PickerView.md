UIPickerView

	<UIPickerViewDelegate, UIPickerViewDataSource>
	
	self.picker.delegate = self;
	self.picker.dataSource = self;
	
	
	// 该方法返回UIPickerView需要多少个组件（有多少列）
	-(NSInteger)numberOfComponentsInPickerView:(UIPickerView *)pickerView{
		return 1;  
	}
	
	// 该方法返回指定组件包含多少行
	-(NSInteger) pickerView:(UIPickerView *)pickerView numberOfRowsInComponent:(NSInteger)component{
		return [pickerArray count];  
	}
	
	// // //		
	Components : 表示有多少列
	RowsInComponent : 表示列有多少行
	
	// 该方法根据指定的行号返回该行的标题
	-(NSString*) pickerView:(UIPickerView *)pickerView titleForRow:(NSInteger)row forComponent:(NSInteger)component{
		return [pickerArray objectAtIndex:row];  
	}
	
	// 当picker view需要给指定的component.row指定view时,调用此函数.返回值为用作row内容的view
	- (UIView *)pickerView:(UIPickerView *)pickerView viewForRow:(NSInteger)row forComponent:(NSInteger)component reusingView:(UIView *)view
	
```objectivec
- (UIView *)pickerView:(UIPickerView *)pickerView viewForRow:(NSInteger)row forComponent:(NSInteger)component reusingView:(UIView *)view {
	UILabel *myView = nil;
	
	myView = [[UILabel alloc] initWithFrame:CGRectMake(0.0, 0.0, 100, 30)]];
	myView.textAlignment = UITextAlignmentCenter;
	myView.text = @"This is a String.";
	myView.font = [UIFont systemFontOfSize:15];
	myView.backgroundColor = [UIColor clearColor];
	
	return myView;
}
```
	
	// 当用户选择某个row时,picker view调用此函数
	-(void) pickerView:(UIPickerView *)pickerView didSelectRow:(NSInteger)row inComponent:(NSInteger)component

	
	// 返回指定的component中行数
	- (NSInteger) numberOfRowsInComponent:(NSInteger)component
	
	// 由picker view需要row的高度时
	- (CGFloat) pickerView:(UIPickerView *)pickerView rowHeightForComponent:(NSInteger)component
	// 当picker view需要row的宽度时
	- (CGFloat)pickerView:(UIPickerView *)pickerView widthForComponent:(NSInteger)component

	
	// 在代码指定要选择的某component的某row
	[picker selectRow:1 inComponent:0 animated:FALSE];
	// 返回指定列被选中行的序号,若无row被选中,则返回-1
	[picker selectRowInComponent:0];
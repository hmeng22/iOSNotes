# UISegmentedControl



点击事件

	[segmentedControl addTarget:self action:@selector(segmentAction:) forControlEvents:UIControlEventValueChanged];

	-(void)segmentAction:(UISegmentedControl *)Seg
	{
		NSInteger index = Seg.selectedSegmentIndex;
		
		// 能获得的属性
		imageForSegmentAtIndex
		titleForSegmentAtIndex
		widthForSegmentAtIndex
		segmentedControl.numberOfSegments
	}
	
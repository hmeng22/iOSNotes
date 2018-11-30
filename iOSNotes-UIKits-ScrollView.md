## Scroll View

1. set the contentSize property to the size of the scrollable content.
2. add a view or views that are displayed and scrolled by the scroll view.

```objectivecscrollView.contentSize=CGSizeMake(1280,960);

CGRect fullScreenRect=[[UIScreen mainScreen] applicationFrame];scrollView=[[UIScrollView alloc] initWithFrame:fullScreenRect];scrollView.contentSize=CGSizeMake(320,758);
// do any further configuration to the scroll view
// contentInset is UIEdgeInsets with top, bottom, left, right.
scrollView.contentInset=UIEdgeInsetsMake(30.0,0.0,30.0,0.0);
scrollView.scrollIndicatorInsets=UIEdgeInsetsMake(30.0,0.0,30.0,0.0);// add a view, or views, as a subview of the scroll view.
```
```objectivec
1. setContentOffset:animated: method scrolls the content to the specified content offset
2. scrollRectToVisible:animated:

the delegate receives a single scrollViewDidScroll: message
When the animation is complete, the delegate receives a scrollViewDidEndScrollingAnimation: message
```
If the status bar is visible a scroll view can also scroll to the top of the content in response to a tap on the status bar. Your application enables this behavior by implementing the delegate method the scroll view property scrollViewShouldScrollToTop: and return YES. When scrolling is complete, the delegate is sent a scrollViewDidScrollToTop: message

* zooming

		<UIScrollViewDelegate>
		
		self.scrollView.minimumZoomScale=0.5;    	self.scrollView.maximumZoomScale=5.0;    	self.scrollView.contentSize=CGSizeMake(1280, 960);    	self.scrollView.delegate=self;
		viewForZoomingInScrollView: return the view to zoom
		
		when the zooming is finished, the delegate receives a scrollViewDidEndZooming:withView:atScale: message		
	* The setZoomScale:animated: sets the current zoom scale to the specified value.
	* The zoomToRect:animated: method zooms the content in order to fill the specified rectangle.

```objectivec
- (CGRect)zoomRectForScrollView:(UIScrollView *)scrollView withScale:(float)scale withCenter:(CGPoint)center {    CGRect zoomRect;    // The zoom rect is in the content view's coordinates.    // At a zoom scale of 1.0, it would be the size of the    // imageScrollView's bounds.    // As the zoom scale decreases, so more content is visible,    // the size of the rect grows.    zoomRect.size.height = scrollView.frame.size.height / scale;    zoomRect.size.width  = scrollView.frame.size.width  / scale;    // choose an origin so as to get the right center.    zoomRect.origin.x = center.x - (zoomRect.size.width  / 2.0);    zoomRect.origin.y = center.y - (zoomRect.size.height / 2.0);    return zoomRect;}
// This utility method is useful when responding to a double tap in a custom subclass that supports that gesture. To use this method simply pass the relevant UIScrollView instance, the new scale (often derived from the existing zoomScale by adding or multiplying a zoom amount), and the point around which to center the zoom. When responding to a double tap gesture the center point is typically the location of the tap. The rectangle that is returned by the method is suitable for passing to the zoomToRect:animated: method.
```

* paging Mode

		contentSize (height,width)
		set pagingMode property to YES
		UIPageControl
		
```objectivec
@property (nonatomic, strong) IBOutlet UIScrollView *scrollView;
@property (nonatomic, strong) IBOutlet UIPageControl *pageControl;
@property (nonatomic, strong) NSMutableArray *viewControllers;

NSUInteger numberPages = self.count;

NSMutableArray *controllers = [[NSMutableArray alloc] init];
for (NSUInteger i = 0; i < numberPages; i++)
{
	[controllers addObject:[NSNull null]];
}
self.viewControllers = controllers;

self.scrollView.pagingEnabled = YES;
self.scrollView.contentSize =
   CGSizeMake(CGRectGetWidth(self.scrollView.frame) * numberPages, CGRectGetHeight(self.scrollView.frame));
self.scrollView.showsHorizontalScrollIndicator = NO;
self.scrollView.showsVerticalScrollIndicator = NO;
self.scrollView.scrollsToTop = NO;
self.scrollView.delegate = self;
    
self.pageControl.numberOfPages = numberPages;
self.pageControl.currentPage = 0;

[self loadScrollViewWithPage:0];
[self loadScrollViewWithPage:1];

- (void)loadScrollViewWithPage:(NSUInteger)page
{
    if (page >= self.contentList.count)
        return;
    
    // replace the placeholder if necessary
    MyViewController *controller = [self.viewControllers objectAtIndex:page];
    if ((NSNull *)controller == [NSNull null])
    {
        controller = [[MyViewController alloc] initWithPageNumber:page];
        [self.viewControllers replaceObjectAtIndex:page withObject:controller];
    }
    
    // add the controller's view to the scroll view
    if (controller.view.superview == nil)
    {
        CGRect frame = self.scrollView.frame;
        frame.origin.x = CGRectGetWidth(frame) * page;
        frame.origin.y = 0;
        controller.view.frame = frame;

        [self addChildViewController:controller];
        [self.scrollView addSubview:controller.view];
        [controller didMoveToParentViewController:self];
        
        NSDictionary *numberItem = [self.contentList objectAtIndex:page];
        controller.numberImage.image = [UIImage imageNamed:[numberItem valueForKey:kImageKey]];
        controller.numberTitle.text = [numberItem valueForKey:kNameKey];
    }
}

// at the end of scroll animation, reset the boolean used when scrolls originate from the UIPageControl
- (void)scrollViewDidEndDecelerating:(UIScrollView *)scrollView
{
    // switch the indicator when more than 50% of the previous/next page is visible
    CGFloat pageWidth = CGRectGetWidth(self.scrollView.frame);
    NSUInteger page = floor((self.scrollView.contentOffset.x - pageWidth / 2) / pageWidth) + 1;
    self.pageControl.currentPage = page;
    
    // load the visible page and the page on either side of it (to avoid flashes when the user starts scrolling)
    [self loadScrollViewWithPage:page - 1];
    [self loadScrollViewWithPage:page];
    [self loadScrollViewWithPage:page + 1];
    
    // a possible optimization would be to unload the views+controllers which are no longer visible
}

- (void)gotoPage:(BOOL)animated
{
    NSInteger page = self.pageControl.currentPage;

    // load the visible page and the page on either side of it (to avoid flashes when the user starts scrolling)
    [self loadScrollViewWithPage:page - 1];
    [self loadScrollViewWithPage:page];
    [self loadScrollViewWithPage:page + 1];
    
	// update the scroll view to the appropriate page
    CGRect bounds = self.scrollView.bounds;
    bounds.origin.x = CGRectGetWidth(bounds) * page;
    bounds.origin.y = 0;
    [self.scrollView scrollRectToVisible:bounds animated:animated];
}

- (IBAction)changePage:(id)sender
{
    [self gotoPage:YES];    // YES = animate
}
```


* views

```objectivec
self.scrollContentView.contentSize = CGSizeMake(ScreenW * 4, 0);

for (int i=0; i<4; i++) {
	UIViewController *vc = ...;
	[self addChildViewController:vc];
	vc.view.frame = CGRectMake(i * ScreenW, 0, ScreenW, ScreenH - 90);
}

[self.scrollContentView setContentOffset:CGPointMake(ScreenW * selectedIndex, 0) animated:YES];
```
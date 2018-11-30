# iAd

You add banner or full-screen advertisements to your app’s user interface; Apple sells advertising space and delivers ads to fill these spaces. You earn revenue when users view or interact with ads displayed by your app.

## ADBannerView

iTunes Connect -> iAd Network


```objectivec
iAd.framework
#import <iAd/iAd.h>
<ADBannerViewDelegate>

self.adBannerView.delegate = self;

// delegate methods
- (void)bannerViewDidLoadAd:(ADBannerView *)banner
{
	// it is called whenever the iAd view is loaded
	NSLog(@"AD is loaded");
    
   [UIView animateWithDuration:.25f animations:^{
       self.adBanner.alpha = 1.f;
   }];
}

- (BOOL)bannerViewActionShouldBegin:(ADBannerView *)banner willLeaveApplication:(BOOL)willLeave
{
    return YES;
}

- (void)bannerView:(ADBannerView *)banner didFailToReceiveAdWithError:(NSError *)error
{
	// it is called when it fail to receive ads
	NSLog(@"AD is fail");
    
   [UIView animateWithDuration:.25f animations:^{
       self.adBanner.alpha = .0f;
   }];
}

- (void)bannerViewActionDidFinish:(ADBannerView *)banner
{
	
}
```
## UIVisualEffectView

```objectivec
// UIBlurEffectStyleLight
// UIBlurEffectStyleExtraLight
UIVisualEffect *effect = [UIBlurEffect effectWithStyle:UIBlurEffectStyleDark];

UIVisualEffectView *effectView = [[UIVisualEffectView alloc] initWithEffect:effect];
effectView.frame = self.imageView.bounds;
[v addSubview:effectView];
```
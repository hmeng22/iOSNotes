## UICollectionView + Layout

UICollectionView  
	|  
	|-UICollectionViewDataSource  
	|  
	|-UICollectionViewDelegate	  
	|
	|-UICollectionViewLayout : UICollectionViewLayoutAttributes  
	|	|-UICollectionViewFlowLayout  
	|  
	|-UICollectionReusableView  
		|-UICollectionViewCell  
		|-SupplmentaryView  
		|-DecorationView  

```objectivec
<UICollectionViewDataSource>
	- numberOfSectionsInCollectionView:
	- collectionView:numberOfItemsInSection:
	- collectionView:cellForItemAtIndexPath:
```
```objectivec
<UICollectionViewDelegate>
	- collectionView:shouldHighlightItemAtIndexPath:
	- collectionView:didHighlightItemAtIndexPath:
	- collectionView:didUnhighlightItemAtIndexPath:

	- collectionView:shouldSelectItemAtIndexPath:
	- collectionView:didSelectItemAtIndexPath:
	- collectionView:shouldDeselectItemAtIndexPath:
	- collectionView:didDeselectItemAtIndexPath:
```

```objectivec
ViewController

<UICollectionViewDataSource, UICollectionViewDelegate>

@property (strong, nonatomic) UICollectionView *mCollectionView;

self.mCollectionView.dataSource = self;
self.mCollectionView.delegate = self;
// setCollectionViewLayout Custom in IB
    
[self.mCollectionView registerClass:[mCollectionViewCell class] forCellWithReuseIdentifier:@"mCollectionViewCellIdentifier"];
[self.mCollectionView registerClass:[nCollectionViewCell class] forCellWithReuseIdentifier:@"nCollectionViewCellIdentifier"];

// UICollectionViewDataSource methods
- (NSInteger)numberOfSectionsInCollectionView:(UICollectionView *)collectionView

- (NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section

- (UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath
{
	mCollectionViewCell *newCell = [collectionView dequeueReusableCellWithReuseIdentifier:@"mCollectionViewCellIdentifier" forIndexPath:indexPath];
        
   // if cell Views don't update automatically, try set their properties here.
   
   return newCell;
}

// UICollectionViewDelegate methods
- (void)collectionView:(UICollectionView *)collectionView didSelectItemAtIndexPath:(NSIndexPath *)indexPath
```
```objectivec
UICollectionViewLayout

- (CGSize)collectionViewContentSize
{
    return self.collectionView.bounds.size;
}

- (UICollectionViewLayoutAttributes *)layoutAttributesForItemAtIndexPath:(NSIndexPath *)indexPath
{
	UICollectionViewLayoutAttributes *attributes = [UICollectionViewLayoutAttributes layoutAttributesForCellWithIndexPath:indexPath];
	    
	CGFloat contentWidth = self.collectionView.bounds.size.width;
	    
	float squareSize = contentWidth/10;
	float x = squareSize*(indexPath.item%9+1);
	float y = squareSize*(indexPath.item/9+1);
	   
	attributes.center = CGPointMake(x, y);
	attributes.size = CGSizeMake(squareSize, squareSize);
	    
	return attributes;
}

- (NSArray *)layoutAttributesForElementsInRect:(CGRect)rect
{
    NSMutableArray *attributes = [NSMutableArray array];
    
    for (NSInteger i = 0 ; i < [self.collectionView numberOfItemsInSection:0]; i++) {
        NSIndexPath *indexPath = [NSIndexPath indexPathForItem:i inSection:0];
        [attributes addObject:[self layoutAttributesForItemAtIndexPath:indexPath]];
    }
    
    return attributes;
}
```
```objectivec
UICollectionViewCell

- (id)initWithFrame:(CGRect)frame
{
    self = [super initWithFrame:frame];
    if (self) {
        // Initialization code
        self.mLabel = [[UILabel alloc] init];
        
        // ...
        
        [self.contentView addSubview:self.mLabel];
    }
    return self;
}

-(void)layoutSubviews
{
    [super layoutSubviews];
    
    // step 1: 生成六边形路径
    CGFloat longSide = self.bounds.size.width * 0.5 * cosf(M_PI * 30 / 180);
    CGFloat shortSide = self.bounds.size.width * 0.5 * sin(M_PI * 30 / 180);
    UIBezierPath *path = [UIBezierPath bezierPath];
    [path moveToPoint:CGPointMake(0, longSide)];
    [path addLineToPoint:CGPointMake(shortSide, 0)];
    [path addLineToPoint:CGPointMake(shortSide + self.bounds.size.width * 0.5, 0)];
    [path addLineToPoint:CGPointMake(self.bounds.size.width, longSide)];
    [path addLineToPoint:CGPointMake(shortSide + self.bounds.size.width * 0.5, longSide * 2)];
    [path addLineToPoint:CGPointMake(shortSide, longSide * 2)];
    [path closePath];
    
    // step 2: 根据路径生成蒙板
    CAShapeLayer *maskLayer = [CAShapeLayer layer];
    maskLayer.path = [path CGPath];
    
    // step 3: 给cell添加模版
    self.layer.mask = maskLayer;
}
```

### 注意
* UICollectionCell 中的Views:Label无法自动更新时,可尝试在collectionView:cellForItemAtIndexPath:方法中设置属性,来达到更新效果.
* 所有的过程都可以通过代码化实现,过程清晰,不需要使用IB
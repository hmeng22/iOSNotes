## NSClassFromString

```objectivec
NSString *className = @"viewController";
    
if (NSClassFromString(className)) {
    Class aClass = NSClassFromString(className);
    id instance = [[aClass alloc] init];
    
    if ([instance isKindOfClass:[UIViewController class]]) {
        [self.navigationController pushViewController:(UIViewController *)instance animated:YES];
    }
}
```

## NSSelectorFromString

```objectivec
NSString *action = [result objectForKey:@"action"];

SEL sel = NSSelectorFromString(action);

if ([self respondsToSelector:sel]) {
//	[self performSelector:NSSelectorFromString(action)];
//	This may cause "performSelector may cause a leak because its selector is unknown".

    IMP imp = [self methodForSelector:NSSelectorFromString(action)];
    void (*func)(id, SEL) = (void *)imp;
    func(self, sel);
    
    ((void (*)(id, SEL))[self methodForSelector:sel])(self, sel);
}

// Objective-C Runtime : in the shared library /usr/lib/libobjc.A.dylib.
IMP : A pointer to the start of a method implementation.
typedef (id (*IMP)(id, SEL, ...))	

// When the selector takes arguments and return value.
SEL selector = NSSelectorFromString(@"processRegion:ofView:");
IMP imp = [_controller methodForSelector:selector];
CGRect (*func)(id, SEL, CGRect, UIView *) = (void *)imp;
CGRect result = _controller ? func(_controller, selector, someRect, someView) : CGRectZero;

// http://stackoverflow.com/questions/7017281/performselector-may-cause-a-leak-because-its-selector-is-unknown
```

## runtime

```objectivec
void objc_setAssociatedObject(id object, const void *key, id value, objc_AssociationPolicy policy);
id objc_getAssociatedObject(id object, const void *key);
void objc_removeAssociatedObjects(id object);

objc_setAssociatedObject 用于给对象添加关联对象，传入 nil 则可以移除已有的关联对象；
objc_getAssociatedObject 用于获取关联对象；
objc_removeAssociatedObjects 用于移除一个对象的所有关联对象。

objc_AssociationPolicy
	OBJC_ASSOCIATION_ASSIGN	
	OBJC_ASSOCIATION_RETAIN_NONATOMIC	
	OBJC_ASSOCIATION_COPY_NONATOMIC
	OBJC_ASSOCIATION_RETAIN	
	OBJC_ASSOCIATION_COPY
	
objc_setAssociatedObject(self, @selector(associatedObject_assign), associatedObject_assign, OBJC_ASSOCIATION_ASSIGN);
```
```objectivec
@interface UIViewController(Attributes)
@property (nonatomic,strong) NSDictionary * attributes;
@end

@implementation UIViewController(Attributes)

id attributes_key;
-(void)setAttributes:(NSDictionary *)attributes
{
    objc_setAssociatedObject(self, &attributes_key, attributes, OBJC_ASSOCIATION_COPY_NONATOMIC);
}
-(NSDictionary *)attributes{
    return objc_getAssociatedObject(self, &attributes_key);
}
@end
```



## Method Swizzling

```objectivec
static dispatch_once_t onceToken;
dispatch_once(&onceToken, ^{
    Class class = [self class];

    SEL originalSelector = @selector(viewWillAppear:);
    SEL swizzledSelector = @selector(anotherViewWillAppear:);

    Method originalMethod = class_getInstanceMethod(class, originalSelector);
    Method swizzledMethod = class_getInstanceMethod(class, swizzledSelector);

    BOOL success = class_addMethod(class, originalSelector, method_getImplementation(swizzledMethod), method_getTypeEncoding(swizzledMethod));
    if (success) {
        class_replaceMethod(class, swizzledSelector, method_getImplementation(originalMethod), method_getTypeEncoding(originalMethod));
    } else {
        method_exchangeImplementations(originalMethod, swizzledMethod);
    }
});
```

### Singleton

```objectivec
+(DBManager *)sharedManager
{
 static DBManager *sharedManager;

 static dispatch_once_t onceToken;
 dispatch_once(&onceToken, ^{
     sharedManager = [[DBManager alloc] init];
 });

 return sharedManager;
}
```

### 初始化:参数firstObj...,nil

```objectivec
#import <UIKit/UIKit.h>
@protocol TopTabBarViewDelegate;

@interface TopTabBarView : UIView
@property (nonatomic,assign) NSInteger selected;
@property (nonatomic,weak) id<TopTabBarViewDelegate> delegate;
- (instancetype)initWithItems:(NSString *)firstObj, ... NS_REQUIRES_NIL_TERMINATION;
@end

@protocol TopTabBarViewDelegate <NSObject>
- (void)tabBarDidSelect:(TopTabBarView *)tabBarView index:(NSInteger)index;
- (void)tabBarDidClickDuplicate:(TopTabBarView *)tabBarView index:(NSInteger)index;
@end


- (instancetype)initWithItems:(NSString *)firstObj, ... NS_REQUIRES_NIL_TERMINATION
{
    NSMutableArray * mArr = [NSMutableArray array];
    [mArr addObject:firstObj];
    
    va_list argumentList;
    va_start(argumentList, firstObj);
    id object;
    while(1){
        object = va_arg(argumentList,id);
        if (object == nil)
            break;
        [mArr addObject:object];
    }
    va_end(argumentList);
    
    
    self = [self initWithFrame:CGRectZero];
    if (self) {
        self.strArray = [NSArray arrayWithArray:mArr];
        [self additemView];
        [self itemDidSelect:0 animated:NO];
    }
    return self;
}
```


## Objective-C

```objectivec
typedef struct objc_class *Class;
typedef struct objc_object *id;
```

```objectivec
objc_msgSend(obj,@selector(doSth));
```
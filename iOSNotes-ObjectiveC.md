# Objective-C

## Basic

### data structures
```objectivec
// reference to an instance of any objective-c class
// 指向一个任何类型对象的指针
id object;

The keyword self is a reference for the current instance.  
The keyword super refers to the parent of the current instance.  
nil represents a null instance.  

Primitives:
Numbers : int, float, double, NSNumber
Boolean : BOOL
String : NSString, NSMutableString
Date : NSDate
binary data : NSData
other : NSIndex, NSValue

/* ------------------------ */
NSNumber *aNumber; 
aNumber = @42; // 42 
aNumber = @3.14; // 3.14 
aNumber = @YES; // 1 
aNumber = @'X'; // 88 

// NSString provides more than 100 methods
NSString *name = @"Meng";
NSString *message = [NSString stringWithFormat:@"Hello %@, you are the %dth visitor.", name, 5];
NSString *newString = [name stringByAppendingString:message];
[NSString stringWithFormat:@"%.2f", floatNumber];



NSMutableString *helloWorld = @"Hello";
[helloWorld appendString:@" World”];

NSDate *now = [NSDate date];
NSDateComponents* components = [[NSDateComponents alloc] init];
[components setWeekday:2];
[components setWeekdayOrdinal:1];
[components setMonth:5];
[components setYear:2015];
NSCalendar *gregorian = [[NSCalendar alloc] initWithCalendarIdentifier:NSGregorianCalendar];
NSDate *date = [gregorian dateFromComponents:components];

NSTimeInterval seconds ;

@property (nonatomic) NSDateFormatter *dateFormatter;
self.dateFormatter = [[NSDateFormatter alloc] init];
[self.dateFormatter setDateFormat:@"yyyyMMddHHmmss”];

NSTimeInterval current = [[NSDate date] timeIntervalSince1970];
NSTimeInterval recorded = current - startTime;
/* ------------------------ */

Collections:
Array : NSArray, NSMutableArray
	数组：每个元素有唯一序号，元素可以重复
Set : NSSet, NSMutableSet
	集合：元素没有序号，绝不重复（若加入相同的元素会覆盖之前的）
Dictionary : NSDictionary, NSMutableDict
	键值对（key－value），每个元素包含一个Key和一个Value
IndexSet : NSIndexSet – NSMutableIndexSet 

Special:
executable code : block
```

### class
```objectivec
// represents an objective-c class
Class aClass;
// reference to an instance of a specific objective-c class
// 单一继承，所有方法public，所有成员变量protected
Person *aPerson;

NSObject
* Get the instance class
	Class aClass = [anObject class];
* Checks if the object is an instance of given class
	if([anObject isKindOfClass:[UIControl class]]) {…}
* Checks if the object is an immediate instance of given class
	if([anObject isMemberOfClass:[NSString class]]) {…}


/* ------------------------------------ */
Class Definition: Header

#import <Foundation/Foundation.h> 

@interface Person : NSObject { 

NSString *name;
int age; 
} 

// accessor for name (note: no “get” for getter!)
- (NSString *)name;
- (void)setName:(NSString *)newName;
// accessor for age
- (int)age;
- (void)setAge:(int)newAge;
// actions 

- (BOOL)isAllowedToVote;
- (void)castBallot;
@end

/* ------------------------------------ */
Class Definition: Implementation

#import "Person.h"

@implementation Person

- (int)age {
  return age;
} 

- (void)setAge:(int)value {
  age = value;
} 

//... and other methods
@end 
/* ------------------------------------ */

```
```objectivec
-(id)initSomething {
	self = [super init];
	
	return self;
}
```

### Allocation and Initialization
When memory is allocated all ivars are set to zero (objects are set to nil).  
The class is responsible to provide proper initializers to initialize the instance with desired values.  
By convention : all initializers methods must start with “init”.  
All classes that have instance variables should provide at least one initializer.  
An initializer must initialize all ivars of the receptor and returns its own instance.  
By convention : the initializer should be called only once.  

`AClass *anObject = [[AClass alloc] init];`
`[anObject someMethod];`

Rules to implement an initializer:

* By convention, the method should start with the name init
* The method must return id
* If the class has more than one initializer it is required to state which one is the designated initializer. This is the initializer that all other initializers should invoke.
* The designated initializer is the only one to invoke the parent initializer.
* Check if the result is nil.
* The initializer must always return self.

### @property
属性, automatically generated setter and getter methods.

	@property (nonatomic) <type> <property name>
	@property (strong or weak) <type which is a pointer to an object> <property name>
	@property (getter=<getter name>) designated getter method
Invoking setting and getter using dot notation, e.g., self.name …

Getters and Setters

	getter method name is equal to the ivar.
	setter : -(void)set<IVarName>:(IVarType)value
	dot syntax : object.properties

在声明.h文件中声明setter和getter

### @synthesize

	合成，在.m文件中，_varName对应.h文件中property变量，统一变量
在实现.m文件中声明setter和getter

### method
Method declaration:  
`MethodType (ReturenType)namePart1:(Type)param1 namePart2:(Type)param2, …;`
`(方法的数据类型) 函数名: (参数1数据类型) 参数1的数值的名字 参数2的名字: (参数2数据类型) 参数2值的名字 …. ;`

Method type:

	Use the - sign for instance methods
	Use the + sign for class methods
	An object is always represented by a pointer

Getters and Setters:  

	getter method name is equal to the ivar.
	setter : -(void)set<IVarName>:(IVarType)value
	dot syntax : object.properties

In Objective-C method invocation follows the dynamic binding mechanism.

1. The runtime checks if the rector implements the method invoked. If the method is founded it is executed.
2. If the method is not founded, the message is sent to the receptor’s parent.
3. The receptor hierarchy is traversed until one of the following conditions is verified:
	1. the method is founded and invoked
	2. the method is not founded when reaching the hierarchy top and an exception is thrown


### message
```objectivec
// message without argument
[receiver message]
// message with single argument
[receiver message:argument]
// message with multiple arguments
[receiver message:arg1 argument2:arg2]
// receiving a return value
int status = [receiver message];
// message with a variable number of arguments
[receiver makeGroup:group, memberOne, memberTwo, memberThree];
```

### selector
SEL is a data type called selector that represents a method (similar to a function pointer).

```objectivec
// create a selector for a method:
SEL mySelector = @selector(myMethod:);
// check if the selector can be performed on object
if([object respondsToSelector:mySelector]) {
  // perform the selector on object with a parameter
  [object performSelector:mySelector withObject:self];
}
```

### delegate
委托, 触发事件后, 一个类自己不处理, 然别人另一个类来处理

```objectivec
@protocol oneDelegate <NSObject>
@required
- (void)requiredDelegateMethod;
@optional
- (void)optionalDelegateMethod;
@end

@interface oneObject : NSObject
@property (nonatomic, weak) id <oneDelegate> delegate;
@end

@implementation oneObject

- (void)exampleFunc {
	[delegate respondsToSelector:@selector(optionalDelegateMethod)];
}

@end
```

### outlet
* allow graphic controls to be treated as properties
* variables that reference views and provide access to their properties

### protocols
协议, 协议要求实现的方法一定要实现, 类似java的interface

* To declare methods that others are expected to implement
* To declare the interface to an object while concealing its class
* To capture similarities among classes that are not hierarchically related

```objectivec
// MyServiceDelegate.h (or MyService.h)
@protocol MyServiceDelegate
// required protocol method
- (void)myService:(MyService *)myService
        didUpdateWithResult:(id)object;
// optional methods
@optional 
... 
@end

// MyController.h
@interface MyController : NSObject <MyServiceDelegate> {}
@end
// For Objects:
id<MyServiceDelegate> anObject;
```

### Enumeration
```objectivec
// we have an array of Person objects
NSArray *people = ...;
// declare person variable outside the for loop
Person *person;
// fast enumeration of the people array
for(person in people) {
  // do something with all persons in the array
  [person castBallot];
}
```

### 动态生成一个类
```objectivec
Class *c = NSClassFromString(@"Person");
```
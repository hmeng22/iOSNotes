# Concurrency

Concurrency is the notion of multiple things happening at the same time.

Grand Central Dispatch (GCD) ; Operation queues

* Serial : Serial queues (also known as private dispatch queues) execute one task at a time in the order in which they are added to the queue.
* Concurrent : Concurrent queues (also known as a type of global dispatch queue) execute one or more tasks concurrently, but tasks are still started in the order in which they were added to the queue.
* The main dispatch queue is a globally available serial queue that executes tasks on the application’s main thread.

```objectivec
dispatch_queue_t queue = dispatch_queue_create("myQueue",NULL);

dispatch_get_current_queue
dispatch_get_main_queue : get the serial dispatch queue associated with main thread
dispatch_get_global_queue : get any of the shared concurrent queues
```
```objectivec
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{  
    // 耗时的操作
    
    // 主线程更新UI
    dispatch_async(dispatch_get_main_queue(), ^{  
        // 更新界面  
    });
});
```

GCD有三种队列:

1. 主线程队列: dispatch_queue_t queue =dispatch_get_main_queue();
2. 并行队列:全局分发队列: dispatch_queue_t queue =dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT,0);
3. 串行队列:私有分发队列: dispatch_queue_t queue =dispatch_queue_create("queue",参数); //串行队列按顺序在同一个时间只执行一个任务 

		dispatch_queue_create的第二个参数：
			1）DISPATCH_QUEUE_SERIAL(串行) : private dispatch queues,同时只执行一个任务. 多个Serial queue各自同步执行,不同Serial queue之间并发执行
			2）DISPATCH_QUEUE_CONCURRENT(并发) : 并发地执行多个任务,完成顺序随机.

两种执行方式:

1. 同步执行,主线程会被阻塞,可以理解为Block的执行是主线程上进行的: dispatch_sync(dispatch_queue_t queue,dispatch_block_t block);
2. 异步执行,与同步执行相反,主线程不会被阻塞: dispatch_async(dispatch_queue_t queue,dispatch_block_t block);
	2. 前面任务结束后执行,后面的任务等它完成后执行: dispatch_barrier_async
	3. 一组任务完成后: dispatch_group_async


* 延时执行 : dispatch_after 无法取消
dispatch_time_t time = dispatch_time(DISPATCH_TIME_NOW, (int64_t)(5.0 * NSEC_PER_SEC));//表示当前时间后的5秒  

dispatch_after(time, dispatch_get_main_queue(), ^(void){  
   //执行代码  
});


* 重复执行线程 : dispatch_apply

以下代码会执行4次
dispatch_apply(4, DISPATCH_QUEUE_PRIORITY_DEFAULT, ^(size_t index) {
    // index则为执行的次数 0开始递增
    NSLog(@"one - %ld", index);
});
index参数为执行的次数，从0开始递增。


其中需要注意的是，每次执行都会新开辟一条子线程，因为是异步的原因，它们不会是顺序的。

[657:159159] one - 0, thread - <NSThread: 0x100110b50>{number = 1, name = main}
[657:159191] one - 2, thread - <NSThread: 0x103800000>{number = 2, name = (null)}
[657:159192] one - 3, thread - <NSThread: 0x100112b90>{number = 3, name = (null)}
[657:159190] one - 1, thread - <NSThread: 0x100501180>{number = 4, name = (null)}


* 一次性执行的方法 : dispatch_once
dispatch_once_t once;
dispatch_once(&once, ^{
    NSLog(@"once - %@", [NSThread currentThread]); // 主线程
});
以上代码执行一次，如果你把它放到循环的地方，运行时就会崩溃。

 


3) 并发串行执行例子

使用GCD也能创建串行队列，具体代码如下：

dispatch_queue_t queue = dispatch_queue_create("queue", DISPATCH_QUEUE_CONCURRENT);

// 线程A
dispatch_async(queue, ^{
    [NSThread sleepForTimeInterval:1];
    NSLog(@"sleep async - %@", [NSThread currentThread]);
});

// 线程B
dispatch_barrier_async(queue, ^{
    [NSThread sleepForTimeInterval:3];
    NSLog(@"sleep barrier2 - %@", [NSThread currentThread]);
});

// 线程C
dispatch_async(queue, ^{
    NSLog(@"async");
});
复制代码
运行效果：以上会先执行 线程A－》线程B－》线程C，它是一个串行队列。


4）GCD群组通知：dispatch_group_t

GCD的高级用法，等所有线程都完成工作后，再作通知。

复制代码
// 创建群组
dispatch_group_t group = dispatch_group_create();

// 线程A
dispatch_group_async(group, dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    NSLog(@"group1");
    [NSThread sleepForTimeInterval:2];
});

// 线程B
dispatch_group_async(group, dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    NSLog(@"group2");
});

// 待群组里的线程都完成之后调用的通知
dispatch_group_notify(group, dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    NSLog(@"group success");
});
复制代码
群组里的线程也是并行队列。线程A和线程B都执行完之后，会调用通知打印group success。

 

5）GCD实现计时器

复制代码
__block int time = 30;
CGFloat reSecond = 1.0;
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
dispatch_source_t timer = dispatch_source_create(DISPATCH_SOURCE_TYPE_TIMER, 0, 0, queue);
dispatch_source_set_timer(timer, DISPATCH_TIME_NOW, reSecond * NSEC_PER_SEC, 0);
dispatch_source_set_event_handler(timer, ^{
    time--;
    NSLog(@"%d", time);
    if (time == 0) {
        dispatch_source_cancel(timer);
    }
});
dispatch_resume(timer);
复制代码
代码效果：创建了一个计时器，计时器运行30秒，每过一秒会调用一次block，我们可以在block里面写代码。因为dispatch_source_t默认是挂起状态，因此我们使用时需要使用dispatch_resume方法先恢复，不然线程不会执行。


6）后台运行

使用block的另一个好处是可以让程序在后台较久地运行。在以前，当应用被按Home键退出后，应用仅有最多5秒的时间做一些保存或清理资源的工作。 但是如果使用GCD，你可以让你的应用最多有10分钟的时间在后台长久运行。这个时间可以用来做各种事情，包括清理本地缓存、发送统计数据等工作。

复制代码
AppDelegate.h
@interface AppDelegate ()

@property (assign, nonatomic) UIBackgroundTaskIdentifier backGroundUpdate;

@end

AppDelegate.m
- (void)applicationDidEnterBackground:(UIApplication *)application {
    [self beginBackGroundUpdate];
   // 需要长久运行的代码
    [self endBackGroundUpdate];
}

- (void)beginBackGroundUpdate
{
    self.backGroundUpdate = [[UIApplication sharedApplication] beginBackgroundTaskWithExpirationHandler:^{
        [self endBackGroundUpdate];
    }];
}

- (void)endBackGroundUpdate
{
    [[UIApplication sharedApplication] endBackgroundTask:self.backGroundUpdate];
    self.backGroundUpdate = UIBackgroundTaskInvalid;
}
复制代码
建议大家在真机上测试，因为笔者在模拟器测试了24分钟还有效。
 


# NSObject

NSObject提供了以performSelector为前缀的一系列方法,它允许用户在指定的线程、什么时间执行某个方法的调用.

在当前线程中执行方法:

	- (id)performSelector:(SEL)aSelector;  
	- (id)performSelector:(SEL)aSelectorwithObject:(id)object;  
	- (id)performSelector:(SEL)aSelectorwithObject:(id)object1 withObject:(id)object2;  
	- (void)performSelector:(SEL)aSelector withObject:(id)anArgument afterDelay:(NSTimeInterval)delay;//delay为延迟多少秒调用  

在主线程中执行方法:

	- (void)performSelectorOnMainThread:(SEL)aSelector withObject:(id)argwaitUntilDone:(BOOL)wait modes:(NSArray *)array;  
	- (void)performSelectorOnMainThread:(SEL)aSelector withObject:(id)argwaitUntilDone:(BOOL)wait;  

在指定线程中执行方法:

	- (void)performSelector:(SEL)aSelectoronThread:(NSThread *)thr withObject:(id)arg waitUntilDone:(BOOL)waitmodes:(NSArray *)array;  
	- (void)performSelector:(SEL)aSelectoronThread:(NSThread *)thr withObject:(id)arg waitUntilDone:(BOOL)wait;  

在后台线程中执行方法:

	- (void)performSelectorInBackground:(SEL)aSelectorwithObject:(id)arg;  

取消延迟调用的方法:

	+ (void)cancelPreviousPerformRequestsWithTarget:(id)aTarget selector:(SEL)aSelectorobject:(id)anArgument;  
	+ (void)cancelPreviousPerformRequestsWithTarget:(id)aTarget;  


# Timer

A timer provides a way to perform a delayed action or a periodic action. The timer waits until a certain time interval has elapsed and then fires, sending a specified message to a specified object.

* Bound to run loop of the current thread
* Fires once or regularly

```objectivec
-(void)targetMethod:(NSTimer *)timer {
	NSLog(@"This is a timer method");
}

// one-off timer
[NSTimer scheduledTimerWithTimeInterval:1.0         target:self         selector:@selector(targetMethod:)         userInfo:nil         repeats:NO];
         
// repeating timer
NSTimer *timer = [NSTimer scheduledTimerWithTimeInterval:1.0         target:self         selector:@selector(targetMethod:)         userInfo:nil         repeats:YES];
[timer fire];
[timer invalidate];

// on date timer
NSDate *fireDate = [NSDate dateWithTimeIntervalSinceNow:1.0];NSTimer *timer = [[NSTimer alloc] initWithFireDate:fireDate                                  interval:0.5                                  target:self                                  selector:@selector(targetMethod:)                                  userInfo:[self userInfo]                                  repeats:YES];
NSRunLoop *runLoop = [NSRunLoop currentRunLoop];
[runLoop addTimer:timer forMode:NSDefaultRunLoopMode];
[timer invalidate];
```

```objectivec
performSelector:withObject:afterDelay:
performSelectorOnMainThread:withObject:waitUntilDone:
cancelPreviousPerformRequestsWithTarget:
```
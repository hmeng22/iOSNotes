# Server Interactions

## Request

```objectivec
NSString(NSData, NSArray, NSDictionary
+(id)stringWithContentOfURL:(NSURL*)url usingEncoding:(NSStringEncoding*)enc error:(NSError*)error

NSURLConnection : NSURLRequest & NSURLResponse
+(NSData*)sendSynchronousRequest:(NSURLRequest*)request retruningResponse:(NSURLResponse*)response error:(NSError*)error;
```

## NSMutableURLRequest

```objectivec
NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
[request setHTTPMethod:@"GET"];
[request setValue:value forHTTPHeaderField:field];

[NSURLConnection sendAsynchronousRequest:request queue:[NSOperationQueue mainQueue completionHandler:^(NSURLResponse *response, NSData *data, NSError *connectionError) {}
```
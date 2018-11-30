# NSJSONSerialization

An object that may be converted to JSON must have the following properties:

* The top level object is an NSArray or NSDictionary.
* All objects are instances of NSString, NSNumber, NSArray, NSDictionary, or NSNull.
* All dictionary keys are instances of NSString.
* Numbers are not NaN or infinity.

```objectivec
Creating a JSON Object :
+ JSONObjectWithData:options:error:
+ JSONObjectWithStream:options:error:

Creating JSON Data :
+ dataWithJSONObject:options:error:
+ writeJSONObject:toStream:options:error:
+ isValidJSONObject:

```

```objectivec
+ JSONObjectWithData:options:error:

+ dataWithJSONObject:options:error:


NSMutableDictionary *mineDictionary = [[NSMutableDictionary alloc] init];
NSMutableDictionary *yourDictionary = [[NSMutableDictionary alloc] init];
NSMutableArray *jsonArray = [[NSMutableArray alloc] init];
[jsonArray addObject:mineDictionary];
[jsonArray addObject:yourDictionary];
 
NSData *nsdata = [NSJSONSerialization dataWithJSONObject:jsonArray options:NSJSONReadingMutableContainers error:nil];
NSString *jsonString =[[NSString alloc] initWithData:nsdata encoding:NSUTF8StringEncoding];


NSData *jsonData = [jsonString dataUsingEncoding:NSUTF8StringEncoding];
NSArray *jsonArray = [NSJSONSerialization JSONObjectWithData:jsonData options:0 error:nil];
```
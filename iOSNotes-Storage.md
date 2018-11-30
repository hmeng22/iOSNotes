## SandBox

默认情况下, 每个沙盒有3个文件夹:
Documents : iTunes备份或恢复的时候会包括此文件夹
Cache : 
Library : 存储程序的默认设置和其它状态信息
tmp : 临时文件, 重启会丢弃所有tmp文件

注意:模拟器沙盒路径每次都会变化,程序应该存储相对路径,使用时在拼接完整路径

沙盒路径

	/Users/username/Library/Developer/CoreSimulator/Devices/模拟器UDID/data/Containers/Bundle/

```objectivec
获取程序的Home目录: NSString *homeDirectory = NSHomeDirectory();
获取Document, Cache, Library目录: NSArray *paths = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory| NSCachesDirectory| NSLibraryDirectory, NSUserDomainMask, YES);
获取Tmp目录: NSString *tmpDirectory = NSTemporaryDirectory(); 
```
```objectivec
NSArray *pathComponents = [NSArray arrayWithObjects:[NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) lastObject], @"MyAudioMemo.m4a", nil];
NSURL *outputFileURL = [NSURL fileURLWithPathComponents:pathComponents];
```
```objectivec
1. 获取路径
	//获得沙盒中目录的全路径
	NSArray *paths = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);
	NSString *pathString = [paths objectAtIndex:0];

		* NSDocumentDirectory
		* NSLibraryDirectory
		* NSCachesDirectory
		* NSTemporaryDirectory
	
	NSString *fileName =[[pathString objectAtIndex:0] stringByAppendingPathComponent:@"/name.txt"];

2. NSFileManager - directory and file
	NSFileManager *fileManager = [NSFileManager defaultManager];
	// 文件夹
	[fileManager createDirectoryAtPath:pathString withIntermediateDirectories:YES attributes:nil error:nil];
	[fileManager createFileAtPath:fileName contents:nil attributes:nil];
	// 文件
	[fileManager createFileAtPath:path contents:nil attributes:nil];
	[fileManager removeItemAtPath:path error:nil];
	// 文件属性
	NSDictionary *fileAttributes = [fileManager attributesOfItemAtPath:path error:nil];
	// 枚举
	NSArray *fileList = [[NSArray alloc] init];
	// shallow search
	fileList = [fileManager contentsOfDirectoryAtPath:path error:&error];
	// deep search
	NSDirectoryEnumerator *dirEnum = [fileManager enumeratorAtPath:path];
	NSString *file;
	while((file = [dirEnum nextObject])){
		if([file pathExtension] isEqualToString:@"mp3"){
			
		}
	}
	
3. NSFileHandle - read and write
	NSFileHandle *fileHandle ＝ [NSFileHandle fileHandleForReadingAtPath:ourDocumentPath];
	NSFileHandle * fileHandle = [NSFileHandle fileHandleForWritingAtPath:fileName];
	NSFileHandle * fileHandle = [NSFileHandle fileHandleForUpdatingAtPath:fileName];
	
	[fileHandle readDataToEndOfFile];
	[fileHandle seekToEbdOfFile];
	[fileHandle writeData:data];
	[fileHandle closeFile];
	
	[data writeToFile:fileName atomically:YES encoding:NSUTF8StringEncoding error:nil];
	NSData *data = [NSData dataWithContentsOfFile:fileName options:0 error:NULL];
```

## NSCoding

要使用对象归档, 对象必须实现NSCoding协议.

```objectivec
-(void)encodeWithCoder:(NSCoder *)encoder
{
    [aCoder encodeObject:_mixName forKey:@"mixName"];
    [aCoder encodeObject:_tracks forKey:@"tracks"];
    [aCoder encodeObject:[NSNumber numberWithInt:_BPM] forKey:@"BPM"];
    [aCoder encodeObject:[NSNumber numberWithInt:_NPB] forKey:@"NPB"];
}

-(id)initWithCoder:(NSCoder *)encoder
{
    self = [super init];
    
    if (self) {
        _mixName = [aDecoder decodeObjectForKey:@"mixName"];
        _tracks = [aDecoder decodeObjectForKey:@"tracks"];
        _BPM = [[aDecoder decodeObjectForKey:@"BPM"] intValue];
        _NPB = [[aDecoder decodeObjectForKey:@"NPB"] intValue];
    }
    
    return self;
}

[NSKeyedArchiver archiveRootObject:obj toFile:fileFullPath];
obj = [NSKeyedUnarchiver unarchiveObjectWithFile:fileFullPath];
```


## NSJSONSerialization

You use the NSJSONSerialization class to convert JSON to Foundation objects and convert Foundation objects to JSON.

An object that may be converted to JSON must have the following properties:

* The top level object is an NSArray or NSDictionary.
* All objects are instances of NSString, NSNumber, NSArray, NSDictionary, or NSNull.
* All dictionary keys are instances of NSString.
* Numbers are not NaN or infinity.

```objectivec
[NSJSONSerialization dataWithJSONObject: options: error:];  

[NSJSONSerialization JSONObjectWithData: options: error:];
```


## NSXMLParser

```objectivec
NSXMLParser *parser=[[NSXMLParser alloc] initWithData:[xml dataUsingEncoding:NSUTF8StringEncoding]];
[parser setDelegate:self];//设置NSXMLParser对象的解析方法代理  
[parser setShouldProcessNamespaces:NO];  
[parser parse];//开始解析

//发现元素开始符的处理函数  （即报告元素的开始以及元素的属性）   
- (void)parser:(NSXMLParser *)parser   
　　　　　　　　didStartElement:(NSString *)elementName   
　　　　　　　　namespaceURI:(NSString *)namespaceURI   
　　　　　　　　qualifiedName:(NSString *)qName   
　　　　　　　　attributes:(NSDictionary *)attributeDict  
  
//处理标签包含内容字符 （报告元素的所有或部分内容）   
- (void)parser:(NSXMLParser *)parser foundCharacters:(NSString *)string  
  
//发现元素结束符的处理函数，保存元素各项目数据（即报告元素的结束标记）  
- (void)parser:(NSXMLParser *)parser   
　　　　　　　　didEndElement:(NSString *)elementName   
　　　　　　　　namespaceURI:(NSString *)namespaceURI   
　　　　　　　　qualifiedName:(NSString *)qName  
  
//报告解析的结束   
- (void)parserDidEndDocument:(NSXMLParser *)parser  
  
//报告不可恢复的解析错误  
- (void)paser:parserErrorOccured 
```


## Property List

每次操作都会覆盖之前所有内容 : 全部读取,增删改,存储

	create *.plist -> XML file
	create *.h and *.m files : @preperty and @synthesize

```objectivec
NSString *plistPath = [[NSBundle mainBundl] pathForResource:@"*" ofType:@"plist"];
NSData *plistXML = [[NSFileManager defaultManager] contentsAtPath:plistPath];
NSDictionary *temp = (NSDictionary *)[NSPropertyListSerialization              propertyListFromData:plistXML              mutabilityOption:NSPropertyListMutableContainersAndLeaves              format:&format              errorDescription:&errorDesc];
[temp objectForKey:@"key"];


NSString *rootPath = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) objectAtIndex:0];
NSString *plistPath = [rootPath stringByAppendingPathComponent:@"*.plist"];
NSDictionary *plistDict = [NSDictionary dictionaryWithObjects:[NSArray arrayWithObjects: personName, phoneNumbers, nil] forKeys:[NSArray arrayWithObjects: @"Name", @"Phones", nil]];
NSData *plistData = [NSPropertyListSerialization dataFromPropertyList:plistDict format:NSPropertyListXMLFormat_v1_0 errorDescription:&error];
if(plistData) {
	[plistData writeToFile:plistPath atomically:YES];￼￼￼￼￼￼￼} else {
	NSLog(error);
	[error release];
}
```

Abstract Type|XML Element      |Cocoa Class               
-------------|-----------------|--------------------------
array        |\<array>         |NSArray                   
dictionary   |\<dict>          |NSDictionary              
string       |\<string>        |NSString                  
data         |\<data>          |NSData                    
date         |\<date>          |NSDate                    
integer      |\<integer>       |NSNumber(intValue)        
float        |\<real>          |NSNumber(floatValue)      
Boolean      |\<true/>\<false/>|NSNumber(boolValue=YES,NO)


## NSUserDefaults

用来保存应用程序设置,属性,用户保存的数据

NSData, NSString, NSNumber, NSDate, NSArray, NSDictionary

```objectivec
NSUserDefaults *defaults =[NSUserDefaults standardUserDefaults];
NSString *name =@”default string“;
[defaults setObject:firstName forKey:@"name"];
UIImage *image=[[UIImage alloc] initWithContentsOfFile:@"photo.jpg"];
//UIImage对象转换成NSData
NSData *imageData = UIImageJPEGRepresentation(image, 100);
[defaults setObject: imageData forKey:@"image"];
[defaults synchronize];


NSUserDefaults *defaults =[NSUserDefaults standardUserDefaults];
//根据键值取出name
NSString *name = [defaults objectForKey:@"name"];
NSData *imageData = [defaults dataForKey:@"image"];
//NSData转换为UIImage
UIImage *Image = [UIImage imageWithData:imageData];
```


## SQLite

FMDB (https://github.com/ccgus/fmdb)

	FMDB.h
	FMDatabase.h
	FMDatabase.m
	FMDatabaseAdditions.h
	FMDatabaseAdditions.m
	FMDatabasePool.h
	FMDatabasePool.m
	FMDatabaseQueue.h
	FMDatabaseQueue.m
	FMResultSet.h
	FMResultSet.m
	
	// create a database
	FMDatabase *db = [FMDatabase databaseWithPath:@"/tmp/tmp.db"];
	
	// open a database
	if (![db open]) {
    // error 
    return;
	}
	// some operation
	// ...

	[db close];
	
	// update
	[FMDatabase executeUpdate:error:withArgumentsInArray:orVAList:]
	
	// query
	FMResultSet *s = [db executeQuery:@"SELECT * FROM myTable"];
	while ([s next]) {
	    //retrieve values for each record
	}

	// is table exist
	[FMDatabase tableExists:@"tableName"];
	FMResultSet *rs = [self.DB executeQuery:@"select count(*) as 'count' from sqlite_master where type ='table' and name = ?", tableName];
    while ([rs next])
    {
        NSInteger count = [rs intForColumn:@"count"];
        NSLog(@"isTableOK %d", count);
        
        if (0 == count)
        {
            return NO;
        }else
        {
            return YES;
        }
    }
	
	// data
	intForColumn:
	longForColumn:
	longLongIntForColumn:
	boolForColumn:
	doubleForColumn:
	stringForColumn:
	dateForColumn:
	dataForColumn:
	dataNoCopyForColumn:
	UTF8StringForColumnIndex:
	objectForColumn:
	
	// sql
	NSString *sql = @"insert into User (name, password) values (?, ?)";
	[db executeUpdate:sql, user.name, user.password];
	[db executeUpdate:@"INSERT INTO myTable VALUES (?)", [NSNumber numberWithInt:42]];
	
	


iOS - FMDB 

	add libsqlite3.0.dylib
	FMDB.h
	FMDatabase.h
	FMDatabase.m
	FMDatabaseAdditions.h
	FMDatabaseAdditions.m
	FMDatabasePool.h
	FMDatabasePool.m
	FMDatabaseQueue.h
	FMDatabaseQueue.m
	FMResultSet.h
	FMResultSet.m
	#import "FMDB"
	
```objectivec
NSArray *documentsPath = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);
NSString *pathString = [documentsPath objectAtIndex:0];
NSString *fileName = [pathString stringByAppendingString:@"/db.sqlite"];

FMDatabase *db = [FMDatabase databaseWithPath:fileName];

if ([db open]) {

}

[db executeUpdate:@"sql"];
FMResultSet *rs = [db executeQuery:@"sql"];
while ([rs next]) {
	// 读取数据
	[rs stringForColumn:@"columnName"]
	[rs intForColumn:@"columnName"]
}

[db beginTransaction];
...
[db commit];

[db close];


使用NSDictonary作为参数 :
NSString *sql = @"insert into users values(:id, :name, :age)";  
NSDictionary *dict = [NSDictionary dictionaryWithObjectsAndKeys:@"id123", @"id", @"kyfxbl", @"name", 23, @"age", nil];  
[db executeUpdate:sql withParameterDictionary:dict];

使用(?),obj时: obj对象 NSNumber, NSString

select * from games order by id desc limit 1;

// 要包含所有列并且顺序一致
insert into table_name values (v1,v2,v3);
```
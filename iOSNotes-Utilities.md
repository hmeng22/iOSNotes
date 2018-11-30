## 宏定义

```objectivec
// iPad or iPhone 
#define deviceType (UI_USER_INTERFACE_IDIOM() ==UIUserInterfaceIdiomPad)? ipad: iphone

//获取系统版本  
#define IOS_VERSION [[[UIDevice currentDevice]systemVersion] floatValue]  
#define CurrentSystemVersion [[UIDevice currentDevice] systemVersion]

//获取当前语言  
#define CurrentLanguage ([[NSLocalepreferredLanguages] objectAtIndex:0])

//获取屏幕 宽度、高度  
#define SCREEN_WIDTH ([UIScreen mainScreen].bounds.size.width)  
#define SCREEN_HEIGHT ([UIScreen mainScreen].bounds.size.height)  

//重写NSLog,Debug模式下打印日志和当前行数  
#if DEBUG  
#define NSLog(FORMAT, ...)fprintf(stderr,"\nfunction:%s line:%d content:%s\n", __FUNCTION__,__LINE__, [[NSString stringWithFormat:FORMAT, ##__VA_ARGS__]UTF8String]);  
#else  
#define NSLog(FORMAT, ...) nil  
#endif
```


## UIDevice

NSString *strName = [[UIDevice currentDevice] name];
NSString *strSysName = [[UIDevice currentDevice] systemName];
NSString *strSysVersion = [[UIDevice currentDevice] systemVersion];
NSString *strModel = [[UIDevice currentDevice] model];
NSString *strLocModel = [[UIDevice currentDevice] localizedModel]


## NSBundle

NSDictionary *dicInfo = [[NSBundle mainBundle] infoDictionary];  
NSString *strAppName = [dicInfo objectForKey:@"CFBundleDisplayName"];  
NSString *strAppVersion = [dicInfo objectForKey:@"CFBundleShortVersionString"];  
NSString *strAppBuild = [dicInfo objectForKey:@"CFBundleVersion"];  


## NSLocale

NSArray *languageArray = [NSLocale preferredLanguages];
NSString *language = [languageArray objectAtIndex:0];

NSLocale *locale = [NSLocale currentLocale];
NSString *country = [locale localeIdentifier];


## Setting Bundle

手机设置里的选项


## Extension

通知栏里的扩展应用


## 功能失效

	1.关闭项目
	2.选择Window->Organizer->Projects
	3.选择失效的那一个工程，右健，Remove from Organizer
	4.打开工程，失效的功能都回来了
	
	
## Info.plist

The property list editor in Xcode by default displays human-readable strings for many keys instead of the actual key name. To display the actual key names as they appear in the Info.plist file, Control-click any of the keys in the editor window and enable the Show Raw Keys/Values item in the contextual window.


## XCode GitHub

cd ~/.ssh
ssh-keygen -t rsa -C "hello@world.com"
xxxSSH(file's name)
passphrase and again
--> xxxSSH and xxxSSH.pub

GitHub - Settings - SSH Key - add xxxSSH.pub

GitHub - new repository 
XCode - check out with https or ssh address


## Debug

程序出现问题:
cd ~/Library/Developer/Xcode/DerivedData
跳转到这个目录下，清除里面所有缓存的数据：
ls rm -fr *
然后重启xocde

真机调试出现问题:
cd ~/Library/Logs/DiagnosticReports/
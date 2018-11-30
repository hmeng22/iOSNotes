# Internationalization & Localization


	1. 选择项目 -> 选择Project,不是Target -> 在Localizations中添加语言

	2. 程序名称的国际化
		InfoPlist.strings -> 右边属性 -> Localization添加语言
		English : CFBundleDisplayName="App";
		Chinese : CFBundleDisplayName="应用";
		NSHumanReadableCopyright = "Copyright (c) 2015 menghua.cn. All rights reserved."
		编辑Info.plist,添加Application has localized display name:boolean=YES;
		
	3. 内容国际化
		新建Strings File并命名Localizable.strings -> 右边属性 -> Localize -> 
		Localizable.strings(english)
		Localizable.strings(chinese)
		添加"Key"="Value";然后使用NSLocalizedString(@"Key",@"Comment")读取内容;
		English : "key"="english"
		Chinese : "key"="chinese"
		
		如果strings文件名不是Localizable,那就要使用NSLocalizedStringFromTable(@"key",@"fileNmae",nil);
		
	4. 图像和其它资源国际化
		方法同内容国际化
		
	5. 导出与导入
		选中项目文件 -> Editor -> Export For Localization
		
* 获取所支持的语言 和 当前使用的语言

	```objectivec
	NSUserDefaults *defaults = [ NSUserDefaults standardUserDefaults ];
	// 取得 iPhone 支持的所有语言设置
	NSArray *languages = [defaults objectForKey : @"AppleLanguages" ];
	NSLog ( @"%@" , languages);
	```
	```objectivec
	NSArray *languages = [NSLocale preferredLanguages];
	NSString *currentLanguage = [languages objectAtIndex:0];
	NSLog ( @"%@" , currentLanguage);
	```
	
	
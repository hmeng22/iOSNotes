## NSString and NSMutableString

```objectivec
//计算一段字符串的长度，两个英文字符占一个长度。  
- (int)countTheStrLength:(NSString*)str  
{  
    int strlength = 0;  
    char* p = (char*)[str cStringUsingEncoding:NSUnicodeStringEncoding];  
    for (int i=0 ; i<[str lengthOfBytesUsingEncoding:NSUnicodeStringEncoding] ;i++) {  
        if (*p) {  
            p++;  
            strlength++;  
        }  
        else {  
            p++;  
        }  
    }  
    return (strlength+1)/2;
}  

//判断NSString字符串是否包含emoji表情  
- (BOOL)stringContainsEmoji:(NSString *)string  
{  
    __block BOOL returnValue =NO;  
    [string enumerateSubstringsInRange:NSMakeRange(0, [string length]) options:NSStringEnumerationByComposedCharacterSequences usingBlock:^(NSString *substring, NSRange substringRange, NSRange enclosingRange, BOOLBOOL *stop) {  
        const unichar hs = [substring characterAtIndex:0];  
        // surrogate pair  
        if (0xd800) {  
            if (0xd800 <= hs && hs <= 0xdbff) {  
                if (substring.length > 1) {  
                    const unichar ls = [substring characterAtIndex:1];  
                    const int uc = ((hs - 0xd800) * 0x400) + (ls - 0xdc00) + 0x10000;  
                    if (0x1d000 <= uc && uc <= 0x1f77f) {  
                        returnValue =YES;  
                    }  
                }  
            }else if (substring.length > 1) {  
                const unichar ls = [substring characterAtIndex:1];  
                if (ls == 0x20e3) {  
                    returnValue =YES;  
                }  
            }else {  
                // non surrogate  
                if (0x2100 <= hs && hs <= 0x27ff) {  
                    returnValue =YES;  
                }else if (0x2B05 <= hs && hs <= 0x2b07) {  
                    returnValue =YES;  
                }else if (0x2934 <= hs && hs <= 0x2935) {  
                    returnValue =YES;  
                }else if (0x3297 <= hs && hs <= 0x3299) {  
                    returnValue =YES;  
                }else if (hs == 0xa9 || hs == 0xae || hs == 0x303d || hs == 0x3030 || hs == 0x2b55 || hs == 0x2b1c || hs == 0x2b1b || hs == 0x2b50) {  
                    returnValue =YES;  
                }  
            }  
        }  
    }];  
    return returnValue;  
}  
```

```objectivec
// 从路径中获得完整的文件名（带后缀）      
exestr = [filePath lastPathComponent];  
NSLog(@"%@",exestr);  
// 获得文件名（不带后缀）  
exestr = [exestr stringByDeletingPathExtension];      
NSLog(@"%@",exestr);  
// 获得文件的扩展类型（不带'.'）  
exestr = [filePath pathExtension];  
NSLog(@"%@",exestr);

NSArray *SeparatedArray =[link componentsSeparatedByString:@"/"];  
```


## NSAttributedString and NSMutableAttributedString

```objectivec
NSMutableAttributedString *mas = [[NSMutableAttributedString alloc] initWithString:question];

[mas beginEditing];

[mas addAttribute:NSForegroundColorAttributeName
           value:[UIColor greenColor]
           range:NSMakeRange(0, mas.length)];

[mas addAttribute:NSFontAttributeName
           value:[UIFont boldSystemFontOfSize:30]
           range:NSMakeRange(0, mas.length)];

NSMutableParagraphStyle *masParagraphStyle = [[NSMutableParagraphStyle alloc] init] ;
[masParagraphStyle setAlignment:NSTextAlignmentRight];
[mas addAttribute:NSParagraphStyleAttributeName
           value:qtparagraphStyle
           range:NSMakeRange(0, mas.length)];

[mas addAttribute:NSLinkAttributeName
            value:[NSURL URLWithString:@"http://www.apple.com/"]
            range:selectedRange];

[mas addAttribute:NSUnderlineStyleAttributeName
            value:[NSNumber numberWithInt:NSSingleUnderlineStyle]
            range:selectedRange]

[mas endEditing];

NSAttributedString *lb= [[NSAttributedString alloc] initWithString: @"\n"];
[text appendAttributedString:lb];
```


## String Transform

```objectivec
[str stringByApplyingTransform(NSStringTransformToLatin, false)];
```


## 加载自定义字体

```objectivec
1. add fonts into the project
2. info.plist : Fonts provided by application : font.ttf
3. Project -> Targets -> Build Phases -> Copy Bundle Resources -> font
4. Use font.
```
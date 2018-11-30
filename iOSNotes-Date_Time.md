## Date

```objectivec
NSDate *now = [NSDate date];
NSDateFormatter *dateFormat = [[NSDateFormatter alloc] init];
[dateFormat setDateFromat:@"yyyy-MM-dd HH:mm:ss"];
NSString *dateString = [dateFormat stringFromDate:now];

S 毫秒
z 时区
```

```objectivec
//获取当前系统时间
-(NSString *)getSystemTime  
{  
    NSDateFormatter* formatter = [[NSDateFormatter alloc] init];  
    [formatter setDateFormat:@"yyyy-MM-dd HH:mm:ss"];  
    NSString* dateTime= [formatter stringFromDate:[NSDate date]];  
   // DLog(@"UnixFormat = %@", [self timeStamp:dateTime]);  
   // DLog(@"TimeFormat = %@", [self formatTime: [self timeStamp:dateTime]]);  
    return dateTime;  
}  
  
//标准时间转化为时间戳  
-(NSString*)timeStamp:(NSString*)str  
{  
    NSDateFormatter* formater = [[NSDateFormatter alloc] init];  
    [formater setDateFormat:@"yyyy-MM-dd HH:mm:ss"];  
    NSDate* date = [formater dateFromString:str];  
    NSString *timeSp = [NSString stringWithFormat:@"%.f", [date timeIntervalSince1970]];  
    return timeSp;  
}  
  
//时间戳转换成标准时间  
- (NSString*)formatTime:(NSString*)str  
{  
    NSDateFormatter* formatter = [[NSDateFormatter alloc]init];  
    [formatter setDateFormat:@"yyyy-MM-dd HH:mm:ss"];  
    NSDate *date = [NSDate dateWithTimeIntervalSince1970:[str intValue]];  
    NSString * dateTime = [formatter stringFromDate:date];  
    return dateTime;  
}  
  
//时间戳转换成标准时间  
- (NSString*)formatTime:(NSString*)str format:(NSString *)format  
{  
    NSDateFormatter* formatter = [[NSDateFormatter alloc]init];  
    [formatter setDateFormat:format];  
    NSDate *date = [NSDate dateWithTimeIntervalSince1970:[str intValue]];  
    NSString * dateTime = [formatter stringFromDate:date];  
    return dateTime;  
}  
  
// 求时差  
- (NSString*)timeDifference:(NSString*)newTime ToOld:(NSString *)oldTime  
{  
    double dateTimeValue = newTime.doubleValue - oldTime.doubleValue;  
    NSString *dateTime = [self timeStringByTimeValue:dateTimeValue];  
    return dateTime;  
} <pre name="code" class="objc">//根据时间戳得到对应的时间字符串  
- (NSString *)timeStringByTimeValue:(double)dateTimeValue  
{  
    NSInteger h = (int)dateTimeValue / 3600;  
    NSInteger m = ((int)dateTimeValue - h * 3600)/60;  
    NSInteger s = (int)dateTimeValue - 33600 * h - m * 60;  
    NSString *h_s = [self getTime:h];  
    NSString *m_s = [self getTime:m];  
    NSString *s_s = [self getTime:s];  
      
    NSString *dateTime = [NSString stringWithFormat:@"%@:%@:%@",h_s,m_s,s_s];  
    return dateTime;  
}  
```
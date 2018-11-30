## iMails
1. 导入MessageUI.framework框架
2. 在需要应用的控制器头文件, 并加入委托:
`#import <MessageUI/MessageUI.h>`
`<MFMailComposeViewControllerDelegate>`

present controller 之前, 先检查设备能否发送邮件, 否则, 会报错, 显示present nil view controller

	if([MFMailComposeViewController cansendMail]){}

一、创建视图控制器:

```objectivec
MFMailComposeViewController *mc = [[MFMailComposeViewController alloc] init];
mc.mailComposeDelegate = self;
```
   
二、设置邮件主题:

```objectivec
[mc setSubject:@"Hello, World!"];
```
   
三、设置收件人, 收件人有三种:

1. 设置主收件人
`[mc setToRecipients:[NSArray arrayWithObjects:@"zhuqi0@126.com","@dave@iphonedevbook.com", nil];`
2. 设置cc
`[mc setCcRecipients:[NSArray arrayWithObject:@"zhuqil@163.com"]];`
3. 设置bcc
`[mc setBccRecipients:[NSArray arrayWithObject:@"secret@gmail.com"]];`
 
四、设置邮件主体, 有两种格式

* 纯文本
`[mc setMessageBody:@"Watson!!!\n\nCome here, I need you!" isHTML:NO];`
* html格式
`[mc setMessageBody:@"<HTML><B>Hello, Joe!</B><BR/>What do you know?</HTML>" isHTML:YES];`
 
五、添加附件

1. 附件的名称
NSString *path = [[NSBundle mainBundle] pathForResource:@"blood_orange"ofType:@"png"];
2. NSData类型的附件
NSData *data = [NSData dataWithContentsOfFile:path]; 
3. mime type
[mc addAttachmentData:data mimeType:@"image/png" fileName:@"blood_orange"];
 
六、视图呈现

```objectivec
[self presentModalViewController:mc animated:YES];
[mc release];
```
 
七、视图控制器的委托方法
邮件视图控制器的委托方法包含在MFMailComposeViewControllerDelegate中, 无论用户是否发送或取消发送, 不论系统是否能够发送邮件, 方法 mailComposeController:didFinishWithResult:error: 都会被调用

```objectivec
- (void)mailComposeController:(MFMailComposeViewController*)controller didFinishWithResult:(MFMailComposeResult)result error:(NSError*)error
{
    switch(result)
    {
        caseMFMailComposeResultCancelled:
            NSLog(@"Mail send canceled...");
            break;
        caseMFMailComposeResultSaved:
            NSLog(@"Mail saved...");
            break;
        caseMFMailComposeResultSent:
            NSLog(@"Mail sent...");
            break;
        caseMFMailComposeResultFailed:
            NSLog(@"Mail send errored: %@...", [error localizedDescription]);
            break;
        default:
            break;
    }
    [self dismissModalViewControllerAnimated:YES];
}
```
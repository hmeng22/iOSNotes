## URL

* File Transfer Protocol (ftp://)* Hypertext Transfer Protocol (http://)* Hypertext Transfer Protocol with encryption (https://) 
* Local file URLs (file:///)
* Data URLs (data://)

## Network

### NSURLSession

NSURLSession
	|- NSURLSessionConfiguration
	|	|- NSURLCache
	|- NSURLSessionTask
		|- NSURLSessionDataTask
		|- NSURLSessionUploadTask
		|- NSURLSessionDownloadTask		

* 通过URL将数据下载到内存
* 通过URL将数据下载到文件系统
* 将数据上传到指定URL
* 在后台完成上述功能

1. Sessions : Default, Ephemeral, Background
2. NSURLSessionConfiguration : 
	* 一般 defaultSessionConfiguration : Cache, Cookie, 鉴权
	* 及时 ephemeralSessionConfiguration : 不使用Cache, Cookie, 鉴权
	* 后台 backgroundSessionConfigurationWithIdentifier : 
3. NSURLRequest -> NSURLSessionTask
	* NSURLSessionDataTask :
	* NSURLSessionUploadTask :
	* NSURLSessionDownloadTask :
4. NSURLSessionDelegate, NSURLSessionTaskDelegate, NSURLSessionDownloadDelegate, NSURLSessionDataDelegate


```objectivec
NSURLSessionConfiguration *defaultConfigObject = [NSURLSessionConfiguration defaultSessionConfiguration];
defaultSession = [NSURLSession sessionWithConfiguration:defaultConfigObject];

NSURLSessionConfiguration *ephemeralConfigObject = [NSURLSessionConfiguration ephemeralSessionConfiguration];
ephemeralSession = [NSURLSession sessionWithConfiguration:ephemeralConfigObject];

NSURLSessionConfiguration *backgroundConfigObject = [NSURLSessionConfiguration backgroundSessionConfiguration:@"backgroundSessionIdentifier"];backgroundSession = [NSURLSession sessionWithConfiguration:backgroundConfigObject];```

```objectivec
NSURL *url = [NSURL URLWithString:@"http://publish.uwo.ca/~hmeng22/"];
NSURLRequest *request = [NSURLRequest requestWithURL:url];

NSURLSessionConfiguration *defaultConfiguration = [NSURLSessionConfiguration defaultSessionConfiguration];
NSURLSession *session = [NSURLSession sharedSession];
NSURLSession *configuredSession = [NSURLSession sessionWithConfiguration:defaultConfiguration];

NSURLSessionDataTask *dataTask = [session dataTaskWith
NSURLSessionUploadTask *uploadTask = [session uploadTaskWithRequest
NSURLSessionDownloadTask *downloadTask = [session downloadTaskWithRequest

1. 普通下载 : downloadTaskWithRequest
2. 可恢复下载 : downloadTaskWithResumeData
3. 后台下载 : 

completionHandler:^(NSURL *location, NSURLResponse *response, NSError *error) {
	[fileManager moveItemAtURL:location toURL:[NSURL fileURLWithPath:	[SOUNDS_DIRECTORY stringByAppendingFormat:@"%@/%@",soundPackage.name,itemName]] error:nil];
}
```

```objectivec
delegate methods : 
data : 
	URLSession:dataTask:didReceiveData:
	URLSession:task:didCompleteWithError:
	
download : 
	URLSession:downloadTask:didFinishDownloadingToURL:
	URLSession:downloadTask:didWriteData:totalBytesWritten:totalBytesExpectedToWrite:
	URLSession:downloadTask:didResumeAtOffset:expectedTotalBytes:
	URLSession:task:didCompleteWithError:

URLSessionDidFinishEventsForBackgroundURLSession:
```

```objectivec
Redirects : A redirect occurs when a server responds to a request by indicating that the client should make a new request to a different URL.

URLSession:task:willPerformHTTPRedirection:newRequest:completionHandler:

connection:willSendRequest:redirectResponse:
```

NSCachedURLResponse :

NSHTTPCookieStorage : NSHTTPCookie : 


### NSURLConnection

	
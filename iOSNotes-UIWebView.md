# UIWebView

UIWebView, NSURLRequest,

	<UIWebViewDelegate>
	[webView setDelegate:self];
	- (void)webViewDidStartLoad:(UIWebView *)webView 网页开始加载的时候调用
	- (void)webViewDidFinishLoad:(UIWebView *)webView 网页加载完成的时候调用
	- (void)webView:(UIWebView *)webView didFailLoadWithError:(NSError *)error 网页加载错误的时候调用


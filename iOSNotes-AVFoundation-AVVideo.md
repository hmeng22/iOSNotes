# AVFoundation

## AVAsset

An AVAsset instance is an aggregated representation of a collection of one or more pieces of media data (audio and video tracks). It provides information about the collection as a whole, such as its title, duration, natural presentation size, and so on.
	
AVAsset is the superclass of other classes used to create asset instances from media at a URL.

Each of the individual pieces of media data in the asset is of a uniform type and called a track. Assets may also have metadata.


* A vital concept in AV Foundation is that initializing an asset or a track does not necessarily mean that it is ready for use.

* You can configure multiple inputs and outputs for a single session, even when the session is running. You can use an instance of a *preview layer* to show the user what a camera is recording.

	### ALAssetsLibrary
	
	### AVAssetWriter
	
	**AVAssetWriter**, **AVAssetWriterInput** and **AVAssetWriterInputPixelBufferAdaptor** to write frames out to an H.264 encoded movie file

## Capture

* An instance of **AVCaptureDevice** to represent the input device
* An instance of a concrete subclass of **AVCaptureInput** to configure the ports from the input device
* An instance of a concrete subclass of **AVCaptureOutput** to manage the output to a movie file or still image
* An instance of **AVCaptureSession** to coordinate the data flow from the input to the output

To show the user a preview of what the camera is recording, you can use an instance of**AVCaptureVideoPreviewLayer** (a subclass of **CALayer**)

You can configure multiple inputs and outputs, coordinated by a single session.

A connection between a capture input and a capture output in a capture session is represented by an **AVCaptureConnection** object.

### AVCaptureSession
#### Configuring a Session	AVCaptureSessionPresetHigh	AVCaptureSessionPresetMedium	AVCaptureSessionPresetLow
		AVCaptureSessionPreset640x480 VGA	AVCaptureSessionPreset1280x720 720p HD.	AVCaptureSessionPresetPhoto Full photo resolution.

```objectivec
[session beginConfiguration];// Remove an existing capture device.// Add a new capture device.// Reset the preset.[session commitConfiguration];
```

### AVCaptureDeviceInput
An AVCaptureDevice object abstracts a physical capture device that provides input data (such as audio or video) to an AVCaptureSession object.

iterates over all the avaliable devices

```objectivec
NSArray *devices = [AVCaptureDevice devices];for (AVCaptureDevice *device in devices) {	NSLog(@"Device name: %@", [device localizedName]);
	if ([device hasMediaType:AVMediaTypeVideo]) {
		if ([device position]==AVCaptureDevicePositionBack) {
			NSLog(@"Device position : back");		￼￼￼} else { 
			NSLog(@"Device position : front");
		}	}
}
```
```objectivec
NSArray *devices = [AVCaptureDevice devicesWithMediaType:AVMediaTypeVideo];NSMutableArray *torchDevices = [[NSMutableArray alloc] init];for (AVCaptureDevice *device in devices) {	if ([device hasTorch] && [device supportsAVCaptureSessionPreset:AVCaptureSessionPreset640x480]) {		[torchDevices addObject:device];	}
}
```

##### Focus Modes

	isFocusModeSupported, focusPointOfInterestSupported, adjustingFocus
	AVCaptureFocusModeLocked: The focal position is fixed.
	AVCaptureFocusModeAutoFocus: The camera does a single scan focus then reverts to locked.
	AVCaptureFocusModeContinuousAutoFocus: The camera continuously autofocuses as needed.
	
##### Exposure Modes

	isExposureModeSupported, exposurePointOfInterestSupported, adjustingExposure
	AVCaptureExposureModeContinuousAutoExposure: The device automatically adjusts the exposure level as needed
	AVCaptureExposureModeLocked: The exposure level is fixed at its current level
	
##### Flash Modes

	isFlashModeSupported, hasFlash
	AVCaptureFlashModeOff: The flash will never fire.	AVCaptureFlashModeOn: The flash will always fire.	AVCaptureFlashModeAuto: The flash will fire dependent on the ambient light conditions.
	
##### Torch Mode

	isTorchModeSupported, hasTorch
	AVCaptureTorchModeOff: The torch is always off.
	AVCaptureTorchModeOn: The torch is always on.	AVCaptureTorchModeAuto: The torch is automatically switched on and off as needed.
	
##### White Balance

	isWhiteBalanceModeSupported, adjustingWhiteBalance
	AVCaptureWhiteBalanceModeLocked: The white balance mode is fixed. 
	AVCaptureWhiteBalanceModeContinuousAutoWhiteBalance: The camera continuously adjusts the white balance as needed.

#### Configuring a Device
```objectivec
NSError *error = nil;if ([device lockForConfiguration:&error]) {
	device.focusMode = AVCaptureFocusModeLocked;
	[device unlockForConfiguration];
} else {
	// Respond to the failure as appropriate.
```
```objectivec
AVCaptureSession *captureSession = <#Get a capture session#>;AVCaptureDeviceInput *captureDeviceInput = <#Get a capture device input#>;if ([captureSession canAddInput:captureDeviceInput]) {
	[captureSession addInput:captureDeviceInput];
} else {
	// Handle the failure.}
```

### AVCaptureOutput
	AVCaptureMovieFileOutput to output to a movie file	AVCaptureVideoDataOutput if you want to process frames from the video being captured, for example,to create your own custom view layer	AVCaptureAudioDataOutput if you want to process the audio data being captured 
	AVCaptureStillImageOutput if you want to capture still images with accompanying metadata
	
```objectivec
AVCaptureSession *captureSession = <#Get a capture session#>;AVCaptureMovieFileOutput *movieOutput = <#Create and configure a movie output#>;if ([captureSession canAddOutput:movieOutput]) {    [captureSession addOutput:movieOutput];} else {    // Handle the failure.}
```

#### AVCaptureMovieFileOutput
```objectivec
AVCaptureMovieFileOutput *aMovieFileOutput = [[AVCaptureMovieFileOutput alloc] init];CMTime maxDuration = <#Create a CMTime to represent the maximum duration#>;aMovieFileOutput.maxRecordedDuration = maxDuration;aMovieFileOutput.minFreeDiskSpaceLimit = <#An appropriate minimum given the quality of the movie format and the duration#>;
```
	startRecordingToOutputFileURL:recordingDelegate:  
	The URL must not identify an existing file, because the movie file output does not overwrite existing resources.
	The delegate must conform to the AVCaptureFileOutputRecordingDelegate protocol, and must implement the captureOutput:didFinishRecordingToOutputFileAtURL:fromConnections:error: method.
	
##### 	metadata

#### AVCaptureVideoDataOutput
```objectivec
AVCaptureVideoDataOutput *videoDataOutput = [AVCaptureVideoDataOutput new];NSDictionary *newSettings = @{ (NSString *)kCVPixelBufferPixelFormatTypeKey : @(kCVPixelFormatType_32BGRA) };videoDataOutput.videoSettings = newSettings;
 // discard if the data output queue is blocked (as we process the still image )[videoDataOutput setAlwaysDiscardsLateVideoFrames:YES];
// create a serial dispatch queue used for the sample buffer delegate as well aswhen a still image is captured// a serial dispatch queue must be used to guarantee that video frames will bedelivered in order// see the header doc for setSampleBufferDelegate:queue: for more informationvideoDataOutputQueue = dispatch_queue_create("VideoDataOutputQueue",DISPATCH_QUEUE_SERIAL);[videoDataOutput setSampleBufferDelegate:self queue:videoDataOutputQueue];AVCaptureSession *captureSession = <#The Capture Session#>;if ( [captureSession canAddOutput:videoDataOutput] )     [captureSession addOutput:videoDataOutput];
```
##### performance consideration for precessing video

#### AVCaptureStillImageOutput

	availableImageDataCVPixelFormatTypes
	availableImageDataCodecTypes
	
	AVCaptureStillImageOutput *stillImageOutput = [[AVCaptureStillImageOutput alloc] init];	NSDictionary *outputSettings = @{ AVVideoCodecKey : AVVideoCodecJPEG};	[stillImageOutput setOutputSettings:outputSettings];

When you want to capture an image, you send the output a captureStillImageAsynchronouslyFromConnection:completionHandler: message. The first argument is the connection you want to use for the capture. You need to look for the connection whose input port is collecting video:

```objectivec
AVCaptureConnection *videoConnection = nil;for (AVCaptureConnection *connection in stillImageOutput.connections) {	for (AVCaptureInputPort *port in [connection inputPorts]) {
		if ([[port mediaType] isEqual:AVMediaTypeVideo] ) {
			videoConnection = connection;
			break;
		}
	}
	if (videoConnection) { break; }
}
```

The second argument to captureStillImageAsynchronouslyFromConnection:completionHandler: is a block that takes two arguments: a CMSampleBuffer opaque type containing the image data, and an error.

```objectivec
[stillImageOutput captureStillImageAsynchronouslyFromConnection:videoConnectioncompletionHandler:^(CMSampleBufferRef imageSampleBuffer, NSError *error) {
	CFDictionaryRef exifAttachments = CMGetAttachment(imageSampleBuffer, kCGImagePropertyExifDictionary,NULL);
	if (exifAttachments) {
		// Do something with the attachments.
	}
	// Continue as appropriate.}];
```

### AVCaptureVideoPreviewLayer
```objectivec
AVCaptureSession *captureSession = <#Get a capture session#>;CALayer *viewLayer = <#Get a layer from the view in which you want to present the preview#>;AVCaptureVideoPreviewLayer *captureVideoPreviewLayer = [[AVCaptureVideoPreviewLayer alloc] initWithSession:captureSession];[viewLayer addSublayer:captureVideoPreviewLayer];
```

	AVLayerVideoGravityResizeAspect: This preserves the aspect ratio, leaving black bars where the video does not fill the available screen area.	AVLayerVideoGravityResizeAspectFill: This preserves the aspect ratio, but fills the available screen area, cropping the video when necessary.	AVLayerVideoGravityResize: This simply stretches the video to fill the available screen area, even if doing so distorts the image.

AVCaptureVideoDataOutput if you want to process frames from the video being captured, frames are presented in the delegate method
```objectivec
captureOutput:didOutputSampleBuffer:fromConnection:
```

Remember that the delegate method is invoked on the queue you specified in setSampleBufferDelegate:queue:; if you want to update the user interface, you must invoke any relevant code on **the main thread**.

显示处理后的 视频效果 需要自定义 Layer
```objectivec
@property (retain) CALayer *cameraViewLayer;
```

## Representations of Media
Video data and its associated metadata are represented in AV Foundation by opaque objects from the Core Media framework. Core Media represents video data using **CMSampleBuffer**. CMSampleBuffer is a Core Foundation-style opaque type; an instance contains the sample buffer for a frame of video data as a Core Video pixel buffer (see **CVPixelBufferRef**). You access the pixel buffer from a sample buffer using **CMSampleBufferGetImageBuffer**:

	CVPixelBufferRef pixelBuffer = CMSampleBufferGetImageBuffer(<#A CMSampleBuffer#>);
	
Metadata. Metadata are stored in a dictionary as an attachment.

	CMSampleBufferRef sampleBuffer = <#Get a sample buffer#>;	CFDictionaryRef metadataDictionary = CMGetAttachment(sampleBuffer, CFSTR("MetadataDictionary", NULL);	if (metadataDictionary) {    // Do something with the metadata.	}
	
## Converting CMSampleBuffer to a UIImage Object

```objectivec
NSData *imageData = [AVCaptureStillImageOutput jpegStillImageNSDataRepresentation:sampleBuffer];
UIImage *image = [[UIImage alloc] initWithData:imageData];
```
```objectivec
CVImageBufferRef imageBuffer = CMSampleBufferGetImageBuffer(sampleBuffer);// Lock the base address of the pixel bufferCVPixelBufferLockBaseAddress(imageBuffer, 0);
// Get the number of bytes per row for the pixel buffervoid *baseAddress = CVPixelBufferGetBaseAddress(imageBuffer);// Get the number of bytes per row for the pixel buffersize_t bytesPerRow = CVPixelBufferGetBytesPerRow(imageBuffer);// Get the pixel buffer width and heightsize_t width = CVPixelBufferGetWidth(imageBuffer);size_t height = CVPixelBufferGetHeight(imageBuffer);// Create a device-dependent RGB color spaceCGColorSpaceRef colorSpace = CGColorSpaceCreateDeviceRGB();// Create a bitmap graphics context with the sample buffer dataCGContextRef context = CGBitmapContextCreate(baseAddress, width, height, 8, bytesPerRow, colorSpace, kCGBitmapByteOrder32Little |kCGImageAlphaPremultipliedFirst);// Create a Quartz image from the pixel data in the bitmap graphics contextCGImageRef quartzImage = CGBitmapContextCreateImage(context);// Unlock the pixel bufferCVPixelBufferUnlockBaseAddress(imageBuffer,0);// Free up the context and color spaceCGContextRelease(context);CGColorSpaceRelease(colorSpace);// Create an image object from the Quartz imageUIImage *image = [UIImage imageWithCGImage:quartzImage];￼￼￼// Release the Quartz imageCGImageRelease(quartzImage);
```

##### UIImage Precessing in iOS


```objectivec
CGImageRef image = [[UIImage imageNamed:@"a"] CGImage];
NSUInteger width = CGImageGetWidth(image);
NSUInteger height = CGImageGetHeight(image);
unsigned char *rawData = malloc(height * width * 4);

// CGColorSpaceRef colorSpace = CGColorSpaceCreateDeviceRGB();
// NSUInteger bytesPerPixel = 4;
// NSUInteger bytesPerRow = bytesPerPixel * width;
// NSUInteger bitsPerComponent = 8;

CGColorSpaceRef colorSpace = CGImageGetColorSpace(imageRef);
NSUInteger bitsPerPixel = CGImageGetBitsPerPixel(imageRef);
NSUInteger bytesPerRow = CGImageGetBytesPerRow(imageRef);
NSUInteger bitsPerComponent = CGImageGetBitsPerComponent(imageRef);

CGContextRef context = CGBitmapContextCreate(rawData, width, height, bitsPerComponent, bytesPerRow, colorSpace, kCGImageAlphaPremultipliedLast | kCGBitmapByteOrder32Big);
CGColorSpaceRelease(colorSpace);
   
CGContextDrawImage(context, CGRectMake(0, 0, width, height), image);

// do something with pixels
// int byteIndex = (bytesPerRow * x) + y * bytesPerPixel;
// rawData[byteIndex + 0,1,2,3] = RGBA;
   
CGImageRef newimage = CGBitmapContextCreateImage(context);
   
CGContextRelease(context);
```


## (CMSampleBufferRef)sampleBuffer to Pixels
```objectivec
CVImageBufferRef imageBuffer = CMSampleBufferGetImageBuffer(sampleBuffer);
CVPixelBufferLockBaseAddress(imageBuffer, 0);
size_t width = CVPixelBufferGetWidth(imageBuffer);
size_t height = CVPixelBufferGetHeight(imageBuffer);
size_t bytesPerRow = CVPixelBufferGetBytesPerRow(imageBuffer);
    
uint8_t *rawData = (uint8_t *)CVPixelBufferGetBaseAddress(imageBuffer);
CVPixelBufferUnlockBaseAddress(imageBuffer, 0);
```



## Image Orientations

	typedef NS_ENUM(NSInteger, UIDeviceOrientation) {
	    UIDeviceOrientationUnknown,
	    UIDeviceOrientationPortrait,            // Device oriented vertically, home button on the bottom
	    UIDeviceOrientationPortraitUpsideDown,  // Device oriented vertically, home button on the top
	    UIDeviceOrientationLandscapeLeft,       // Device oriented horizontally, home button on the right
	    UIDeviceOrientationLandscapeRight,      // Device oriented horizontally, home button on the left
	    UIDeviceOrientationFaceUp,              // Device oriented flat, face up
	    UIDeviceOrientationFaceDown             // Device oriented flat, face down
	};

	typedef NS_ENUM(NSInteger, ALAssetOrientation) {
	    ALAssetOrientationUp,            // default orientation
	    ALAssetOrientationDown,          // 180 deg rotation
	    ALAssetOrientationLeft,          // 90 deg CCW
	    ALAssetOrientationRight,         // 90 deg CW
	    ALAssetOrientationUpMirrored,    // as above but image mirrored along other axis. horizontal flip
	    ALAssetOrientationDownMirrored,  // horizontal flip
	    ALAssetOrientationLeftMirrored,  // vertical flip
	    ALAssetOrientationRightMirrored, // vertical flip
	};
	
## AVKit

```objectivec
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
dispatch_async(queue, ^{
    NSURL *url = [NSURL URLWithString:url];
    AVPlayerItem *playerItem = [AVPlayerItem playerItemWithURL:url];
    AVPlayer *player = [AVPlayer playerWithPlayerItem:playerItem];
    
    AVPlayerViewController *playerViewController = [[AVPlayerViewController alloc] init];
    playerViewController.player = player;

    dispatch_async(dispatch_get_main_queue(), ^{

        [SVProgressHUD dismiss];
        [self presentViewController:playerViewController
                           animated:YES
                         completion:^{
                         }];
    });
});
```

```objectivec
#import <AVKit/AVKit.h>

AVPlayerItem *playerItem = [AVPlayerItem playerItemWithURL:[NSURL fileURLWithPath:[[NSBundle mainBundle] pathForResource:@"video" ofType:@"mov"]]];
//    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(itemDidFinishPlaying:) name:AVPlayerItemDidPlayToEndTimeNotification object:playerItem];

AVPlayer *player = [[AVPlayer alloc] initWithPlayerItem:playerItem];

AVPlayerViewController *playerViewController = [AVPlayerViewController new];

playerViewController.player = player;
[playerViewController.player play];//Used to Play On start

[self presentViewController:playerViewController animated:YES completion:nil];
```
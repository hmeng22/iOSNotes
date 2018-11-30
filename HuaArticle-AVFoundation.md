## Applications

1. devices, AVCaptureSession
2. AVAudioSession and AVAudioPlayer, AVAudioRecorder
3. ALAssetLibrary and AVAssetWriter
4. AVAssetReader
5. Mix

### devices, AVCaptureSession

```objectivec
// All properties
	
// physical devices
@property (retain) AVCaptureDevice *cameraDevice;
@property (retain) AVCaptureDevice *audioDevice;
	
// session
@property (retain) AVCaptureSession *session;
	
// session input port and output
@property (retain) AVCaptureDeviceInput *cameraInput;
@property (retain) AVCaptureDeviceInput *audioInput;
	
@property (retain) AVCaptureStillImageOutput *stillImageOutput;
@property (retain) AVCaptureAudioDataOutput *audioDataOutput;
@property (retain) AVCaptureVideoDataOutput *videoDataOutput;
@property (retain) AVCaptureMovieFileOutput *movieFileOutput;

// session connection
@property (retain) AVCaptureConnection *connection;

// session previewLayer
@property (retain) AVCaptureVideoPreviewLayer *previewLayer;

	
- setupAVCapture
{
	// physical devices
	_cameraDevice = [AVCaptureDevice defaultDeviceWithMediaType:AVMediaTypeVideo];
	/*	AVMediaTypeVideo;
		AVMediaTypeAudio;
		*/
	[_cameraDevice lockForConfiguration:error];
	AVCaptureDeviceFormat *deviceFormat = [_cameraDevice formats];
	AVFrameRateRange *frameRateRange = _deviceFormat.videoSupportedFrameRateRanges;
	_cameraDevice.activeFormat = _deviceFormat;
	_cameraDevice.activeVideoMinFrameDuration = frameRateRange.minFrameDuration;
	_cameraDevice.activeVideoMaxFrameDuration = frameRateRange.minFrameDuration;
	if([_cameraDevice isFocusModeSupported: setFocusMode:AVCaptureFocusModeContinuousAutoFocus])
		[_cameraDevice setFocusMode:AVCaptureFocusModeContinuousAutoFocus];
		/*	AVCaptureFocusModeLocked
			AVCaptureFocusModeAutoFocus
	    	AVCaptureFocusModeContinuousAutoFocus
	    	*/
	if([_cameraDevice isExposureModeSupported:AVCaptureExposureModeAutoExpose])
		[_cameraDevice setExposureMode:AVCaptureExposureModeAutoExpose];
		/*	AVCaptureExposureModeLocked
	    	AVCaptureExposureModeAutoExpose
	    	AVCaptureExposureModeContinuousAutoExposure
			*/
	if([_cameraDevice isWhiteBalanceModeSupported: AVCaptureWhiteBalanceModeContinuousAutoWhiteBalance])
		[_cameraDevice setWhiteBalanceMode: AVCaptureWhiteBalanceModeContinuousAutoWhiteBalance];
		/*	AVCaptureWhiteBalanceModeLocked
	    	AVCaptureWhiteBalanceModeAutoWhiteBalance
	    	AVCaptureWhiteBalanceModeContinuousAutoWhiteBalance
			*/
	if([_cameraDevice hasFlash] && [_cameraDevice isFlashModeSupported:AVCaptureFlashModeAuto])
		[_cameraDevice setFlashMode: AVCaptureFlashModeAuto]
		/*	AVCaptureFlashModeOff
	    	AVCaptureFlashModeOn
	    	AVCaptureFlashModeAuto
			*/
	AVCaptureDevicePosition *cameraDevicePosition = [_cameraDevice position];
	[_cameraDevice unlockForConfiguration];
	
	
	// session
	_session = [[AVCaptureSession alloc] init];
	[_session beginConfiguration];
	if([_session canSetSessionPreset:AVCaptureSessionPreset1280x720])
		[_session setSessionPreset:AVCaptureSessionPreset1280x720
	/*	AVCaptureSessionPresetPhoto;
		AVCaptureSessionPresetHigh;
		AVCaptureSessionPresetMedium;
		AVCaptureSessionPresetLow;
		AVCaptureSessionPreset320x240;
		AVCaptureSessionPreset352x288;
		AVCaptureSessionPreset640x480;
		AVCaptureSessionPreset960x540;
		AVCaptureSessionPreset1280x720;
		AVCaptureSessionPresetiFrame960x540;
		AVCaptureSessionPresetiFrame1280x720;
		*/
	[_session setAutomaticallyConfiguresApplicationAudioSession:YES];
	// if we set NO, we need to setup AVAudioSession by ourselves.
	[_session commitConfiguration];
	
	// session input and output
	_cameraInput = [AVCaptureDeviceInput deviceInputWithDevice: _cameraDevice error:&error];
	if([_session canAddInput:_cameraInput])
	{
		[_session begionConfiguration];
		[_session removeInput:_cameraInput];
		[_session addInput:_cameraInput];
		[_session commitConfiguration];
	}
	NSArray *cameraInputPorts = [_cameraInput ports];
	for(AVCaptureInputPort *port in cameraInputPorts)
		;
		
	/* Still Image
	_stillImageOutput = [[AVCaptureStillImageOutput alloc] init];
	NSDictionary *outputSettings = [[NSDictionary alloc] initWithObjectsAndKeys: AVVideoCodecJPEG, AVVideoCodecKey, kCVPixelFormatType_32BGRA, kCVPixelBufferPixelFormatTypeKey, nil];
	/*	codecKeys : 
		AVVideoCodecH264
		AVVideoCodecJPEG
		pixelFromats : 
		kCVPixelFormatType_420YpCbCr8BiPlanarFullRange
		kCVPixelFormatType_32BGRA
		//
	[_stillImageOutput setOutputSettings:outputSettings];
	if([_session canAddOutput:_stillImageOutput])
		[_session addOutput:_stillImageOutput];
	
	[_session startRunning];
	
	// Capture Action
	[_stillImageOutput captureStillImageAsynchronouslyFromConnection:videoConnection completionHandler: ^(CMSampleBufferRef imageSampleBuffer, NSError *error) {
		NSData *imageData = [AVCaptureStillImageOutput jpegStillImageNSDataRepresentation:imageSampleBuffer];
		UIImage *image = [[UIImage alloc] initWithData:imageData];
	}];
	*/
	
	/* Audio Data
	_audioDataOutput = [[AVCaptureAudioDataOutput alloc] init];
	dispatch_queue_t queue = dispatch_queue_create("Audio Queue", DISPATCH_QUEUE_SERIAL);
	[_audioDataOutput setSampleBufferDelegate:self queue:queue];
	if([_session canAddOutput:_audioDataOutput])
		[_session addOutput:_audioDataOutput];
	
	[_session startRunning];
	
	<AVCaptureAudioDataOutputSampleBufferDelegate>
	- captureOutput:didOutputSampleBuffer:fromConnection:
	{
		CMAudioFormatDescriptionRef format = CMSampleBufferGetFormatDescription(sampleBuffer);
	}
	*/
	
	/* Video Data
	_videoDataOutput = [[AVCaptureVideoDataOutput alloc] init];
	NSDictionary *outputSettings = [[NSDictionary alloc] initWithObjectsAndKeys: kCVPixelFormatType_32BGRA, kCVPixelBufferPixelFormatTypeKey, nil];
	[_videoDataOutput setVideoSettings:outputSettings];
	[_videoDataOutput setAlwaysDiscardsLateVideoFrames:YES];
	dispatch_queue_t queue = dispatch_queue_create("Video Queue", DISPATCH_QUEUE_SERIAL);
	[_videoDataOutput setSampleBufferDelegate:self queue:queue];
	if([_session canAddOutput:_videoDataOutput])
		[_session addOutput:_videoDataOutput];
	
	[_session startRunning];
	
	<AVCaptureVideoDataOutputSampleBufferDelegate>
	- captureOutput:didOutputSampleBuffer:fromConnection:
	{
		CVPixelBufferRef imageBuffer = CMSampleBufferGetImageBuffer(sampleBuffer);
		CIImage *ciImage = [CIImage imageWithCVPixelBuffer:imageBuffer];
		CIContext *ciContext = [CIContext contextWithOptions:nil];
		CGImageRef ref = [ciContext createCGImage:ciImage fromRect:ciImage.extent];
		
		CGImageRelease(ref);
	}
	*/
	
	/* Movie File
	_movieFileOutput = [[AVCaptureMovieFileOutput alloc] init];
	Float64 TotalSeconds = 60;	//Total seconds
	int32_t preferredTimeScale = 30;	//Frames per second
	CMTime maxDuration = CMTimeMakeWithSeconds(TotalSeconds, preferredTimeScale);	//<<SET MAX DURATION
	_movieFileOutput.maxRecordedDuration = maxDuration;
	
	if([_session canAddOutput:_movieFileOutput])
		[_session addOutput:_movieFileOutput];
	
	[_session startRunning];
	
	NSString *outputFilePath = [NSTemporaryDirectory() stringByAppendingPathComponent:[@"movieOutput" stringByAppendingPathExtension:@"mov"]];
	if([[NSFileManager defaultManager] fileExistsAtPath: outputFilePath])
	{
		[[NSFileManager defaultManager] removeItemAtPath:outputFilePath error:error];
	}
	
	// Movie Action
	[_movieFileOutput startRecordingToOutputFileURL:[NSURL alloc] initFileURLWithPath:outputFilePath] recordingDelegate:self];
	
	<AVCaptureFileOutputRecordingDelegate>
	- captureOutput:didFinishRecordingToOutputFileAtURL:fromConnections:error:
	{
		[ALAssetsLibrary writeVideoAtPathToSavedPhotosAlbum:completionBlock:^(){}];
	}
	
	
	[_session stopRunning];
	*/
	
	// session connection
	NSArray *connectionsArray = [AVCaptureOutput connections];
	_connection = [AVCaptureOutput connectionWithmediaType: AVMediaTypeVideo];
	AVCaptureAudioChannel *audioChannel = _connection.audioChannels;
	float avg = audioChannel.averagePowerLevel;
	float peak = audioChannel.peakHoldLevel
	if([_connection supportsVideoOrientation])
		[_connection setVideoOrientation: AVCaptureVideoOrientationLandscapeRight];
		/*	AVCaptureVideoOrientationPortrait
			AVCaptureVideoOrientationPortraitUpsideDown
			AVCaptureVideoOrientationLandscapeRight
			AVCaptureVideoOrientationLandscapeLeft
			*/
	if([_connection supportsVideoMirroring])
		[_connection setVideoMirrored:YES];
	// inputPorts : _connection.inputPorts
	// output : _connection.output
	
	
	// session previewLayer
	_previewLayer = [AVCaptureVideoPreviewLayer layerWithSession:_Session];
	[_previewLayer setFrame:self.view.bounds];
	[_previewLayer setVideoGravity:AVLayerVideoGravityResizeAspect];
	[self.view.layer insertSublayer:_previewLayer atIndex:0];
}
	
- setupOthers
{
	// metadata
	NSMutableArray *metadataArray = [[NSMutableArray alloc] init];
	AVMutableMetadataItem *item = [[AVMutableMetadataItem alloc] init];
	item.keySpace = AVMetadataKeySpaceCommon;
	item.key = AVMetadataCommonKeyLocation;
	CLLocation *location - <#The location to set#>;
	item.value = [NSString stringWithFormat:@"%+08.4lf%+09.4lf/", location.coordinate.latitude, location.coordinate.longitude];
	[metadataArray addObject:item];
}	
```


### AVAudioSession and AVAudioPlayer, AVAudioRecorder

AVAudioSession is more like a 'Controller', it charges all setting. While Media Player plays as 'View' role. 

AVAudioPlayer and AVAudioRecorder setup AVAudioSession automatically.

For recording, we can use AVAudioRecorder, AVCaptureAudioDataOutput, Audio Queue services.

```objectivec
AVAudioSession *audioSession = [AVAudioSession sharedInstance];
[audioSession setCategory:AVAudioSessionCategoryPlayAndRecord error:&error];
/*	AVAudioSessionCategoryAmbient;
	AVAudioSessionCategorySoloAmbient;
	AVAudioSessionCategoryPlayback;
	AVAudioSessionCategoryRecord;
	AVAudioSessionCategoryPlayAndRecord;
	AVAudioSessionCategoryAudioProcessing;
	AVAudioSessionCategoryMultiRoute;
	*/
[audioSession setMode:AVAudioSessionModeDefault error:&error];
/*	AVAudioSessionModeDefault;
	AVAudioSessionModeVoiceChat;
	AVAudioSessionModeGameChat;
	AVAudioSessionModeVideoRecording;
	AVAudioSessionModeMeasurement;
	AVAudioSessionModeMoviePlayback;
	AVAudioSessionModeVideoChat;
	*/
[audioSession setActive:YES error:&error];
float outputVolumeNumber = audioSession.outputVolume;
double sampleRateNumber = audioSession.sampleRate;
int inputChannelsNumber = audioSession.inputNumberOfChannels;
int outputChannelsNumber = audioSession.outputNumberOfChannels;
AVAudioSessionRouteDescription
AVAudioSessionPortDescription
	AVAudioSessionChannelDescription
AVAudioSessionDataSourceDescription
```

一个AVAudioPlayer一次只能播放一个音频
```objectivec
[[AVAudioSession sharedInstance] setCategory:AVAudioSessionCategoryPlayback  error:error];
AVAudioPlayer *audioPlayer = [[AVAudioPlayer alloc] initWithContentsOfURL:[[NSURL alloc] initFileURLWithPath:[[NSBundle mainBundle] pathForResource:@"click" ofType:@"wav"]] error:error];
[audioPlayer prepareToPlay];
[audioPlayer play];

<AVAudioPlayerDelegate>
- audioPlayerDidFinishPlaying:successfully:
- audioPlayerDecodeErrorDidOccur:error:
- (void)audioPlayerBeginInteruption:
- (void)audioPlayerEndInteruption:

多个AVAudioPlayer播放时,为了避免ARC自动回收,可以使用容器把players存起来,播放完成后移除
@property (nonatomic, strong) NSMutableArray *audioPlayerArray;
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        AVAudioPlayer *audioPlayer = [[AVAudioPlayer alloc] initWithContentsOfURL:[NSURL fileURLWithPath:[_library.mixArray objectAtIndex:[indexPath row]]] error:nil];
        [_audioPlayerArray addObject:audioPlayer];
        NSLog(@"%@ ...", _audioPlayerArray);
        [audioPlayer setDelegate:self];
        [audioPlayer prepareToPlay];
        [audioPlayer play];
    });
- (void)audioPlayerDidFinishPlaying:(AVAudioPlayer *)player successfully:(BOOL)flag
{
    [_audioPlayerArray removeObject:player];
    NSLog(@"%@ ...", _audioPlayerArray);
}
```

```objectivec
[[AVAudioSession sharedInstance] setCategory:AVAudioSessionCategoryRecord error:error];
NSDictionary *recordSettings = [[NSDictionary alloc] initWithObjectsAndKeys:[NSNumber numberWithFloat:44100.0], AVSampleRateKey,
[NSNumber numberWithInt:kAudioFormatLinearPCM], AVFormatIDKey,
[NSNumber numberWithInt:2], AVNumberOfChannelsKey,
[NSNumber numberWithInt:16], AVLinearPCMBitDepthKey,
[NSNumber numberWithBool:NO], AVLinearPCMIsBigEndianKey,
[NSNumber numberWithBool:NO], AVLinearPCMIsFloatKey,
[NSNumber numberWithBool:NO], AVLinearPCMIsNonInterleaved,
[NSNumber numberWithInt:AVAudioQualityMax], AVEncoderAudioQualityKey,
nil];
AVAudioRecorder *audioRecorder = [[ AVAudioRecorder alloc] initWithURL:url settings:recordSetting error:&err];

audioRecorder.delegate = self;
[audioRecorder prepareToRecord];
[audioRecorder record];

<AVAudioRecorderDelegate>
- audioRecorderDidFinishRecording:successfully:
```


### ALAssetLibrary and AVAssetWriter

```objectivec
ALAssetsLibrary *library = [[ALAssetsLibrary alloc] init];

[library enumerateGroupsWithTypes:ALAssetsGroupAll usingBlock:^(ALAssetsGroup *group, BOOL *stop) {
	[group setAssetsFilter:[ALAssetsFilter allPhotos]];
	
	int goroupAssetsNumber = [group numberOfAssets];
	NSString *groupName = [group valueForProperty:ALAssetsGroupPropertyName];
	int groupType = [group valueForProperty: ALAssetsGroupPropertyType];
	NSString *groupID = [group valueForProperty: ALAssetsGroupPropertyPersistentID];
	NSURL *groupURL = [group valueForProperty: ALAssetsGroupPropertyURL];
	CGImageRef *groupPosterImage = [group posterImage];
	
	[group enumerateAssetsUsingBlock:^(ALAsset *result, NSUInteger index, BOOL *stop) {
		ALAssetRepresentation *representation = [result defaultRepresentation];

		// Metadata
		NSString *imageUrl = [representation UTI];
		NSDictionary *metaDataDictonary = [representation metadata];
		// Image Attributes
		ALAssetOrientation assetOrientation = [representation orientation];
		float assetScale = [representation scale];
		CGSize *assetDimensions = [representation dimensions];
		NSString *assetFilename = [representation filename];
		// Raw Data
		long long assetSzie = [representation size];
		// URL
		NSURL *assetURL = [representation url];
		
		
		NSString *assetType = [reslut valueForProperty:ALAssetPropertyType];
		CLLocation *assetLocation = [result valueForProperty:ALAssetPropertyLocation];
		NSString *assetDate = [result valueForProperty:ALAssetPropertyDate];
		NSString *assetURL = [reslut valueForProperty:ALAssetPropertyAssetURL];
		
		ALAssets *assetOriginal = [result originalAsset];
		
	}];
} failureBlock:^(NSError *error) {
	
}];

/*	ALAssetsGroupLibrary : assets synced from iTunes
	ALAssetsGroupAlbum
	ALAssetsGroupEvent
	ALAssetsGroupFaces
	ALAssetsGroupSavedPhotos : photos in the Camera Roll
	ALAssetsGroupPhotoStream
	ALAssetsGroupAll
	*/
/*	allAssets
	allPhotos
	allVideos
	*/

// libaray create ALAssetsGroup
[library addAssetsGroupAlbumWithName:GROUP_ALBUM_NAME resultBlock:^(ALAssetsGroup *group) {
	
} failureBlock:^(NSError *error) {
	
}];

// libaray save ALAssets
- writeImageToSavedPhotosAlbum:orientation:completionBlock:

// ALAssets save methods
- writeModifiedImageDataToSavedPhotosAlbum:metadata:completionBlock:
- writeModifiedVideoAtPathToSavedPhotosAlbum:completionBlock:
```

```objectivec

NSURL *fileURL;

AVAssetWriter *assetWriter = [AVAssetWriter assetWriterWithURL:fileURL fileType:AVFileTypeMPEG4 error:error];


AVAssetWriterInput *audioWriterInput = [AVAssetWriterInput assetWriterInputWithMediaType:AVMediaTypeAudio outputSettings:audioOutputSettings];
audioWriterInput.expectsMediaDataInRealTime = YES;

if([assetWriter canAddInput:audioWriterInput])
	[assetWriter addInput:audioWriterInput];


NSDictionary *videoCompression = [NSDictionary dictionaryWithObjectsAndKeys:
[NSNumber numberWithDouble:128.0*1024.0], AVVideoAverageBitRateKey, thenil];
NSDictionary *videoOutputSettings = [NSDictionary dictionaryWithObjectsAndKeys:[NSNumber numberWithInt:1920], AVVideoWidthKey,
[NSNumber numberWithInt:1080], AVVideoHeightKey,
AVVideoCodecH264, AVVideoCodecKey, 
videoCompression, AVVideoCompressionPropertiesKey, nil];

AVAssetWriterInput *videoWriterInput = [AVAssetWriterInput assetWriterInputWithMediaType:AVMediaTypeVideo outputSettings: videoOutputSettings];
videoWriterInput.expectsMediaDataInRealTime = YES;
videoWriterInput.transform = CGAffineTransformMakeRotation(M_PI/2);

NSDictionary *pixelBufferOutputSetting = [NSDictionary dictionaryWithObjectsAndKeys:[NSNumber numberWithInt:kCVPixelFormatType_420YpCbCr8BiPlanarVideoRange], kCVPixelBufferPixelFormatTypeKey, nil];

AVAssetWriterInputPixelBufferAdaptor *pixelBufferAdaptor = [[AVAssetWriterInputPixelBufferAdaptor alloc] initWithAssetWriterInput: videoWriterInput sourcePixelBufferAttributes:pixelBufferOutputSetting];

if([assetWriter canAddInput:videoWriterInput])
	[assetWriter addInput:videoWriterInput];

[assetWriter startWriting];
[assetWriter startSessionAtSourceTime:kCMTimeZero];

// Capture Action
if(videoWriterInput.readyForMoreMediaData)
	[pixelBufferAdaptor appendPixelBuffer:CMSampleBufferGetImageBuffer(sampleBuffer) withPresentationTime:CMTimeMake(frameNumber, VIDEO_FPS)];
	
[assetWriter finishWritingWithCompletionHandler:^{
	// Now data has been wrote into file. We can save file into Assets Libaray.
}];
```


### AVAssetReader

```objectivec
AVAssetReader *assetReader = [[AVAssetReader alloc] initWithAsset:videoAsset error:&error];
NSArray *audioAssetTracks = [videoAsset tracksWithMediaType:AVMediaTypeAudio];
    
NSDictionary* outputSettings = [[NSDictionary alloc] initWithObjectsAndKeys:[NSNumber numberWithInt:kAudioFormatLinearPCM], AVFormatIDKey,
[NSNumber numberWithInt:44100.0],AVSampleRateKey,
[NSNumber numberWithInt: 2],AVNumberOfChannelsKey,
[NSNumber numberWithInt:16],AVLinearPCMBitDepthKey,
[NSNumber numberWithBool:NO],AVLinearPCMIsBigEndianKey,
[NSNumber numberWithBool:NO],AVLinearPCMIsFloatKey,
[NSNumber numberWithBool:NO],AVLinearPCMIsNonInterleaved, nil];

NSDictionary *outputSettings = [[NSDictionary alloc] initWithObjectsAndKeys:[NSNumber numberWithInt:kCVPixelFormatType_420YpCbCr8BiPlanarFullRange], kCVPixelBufferPixelFormatTypeKey, nil];

AVAssetReaderTrackOutput *assetReaderTrackOutput = [[AVAssetReaderTrackOutput alloc] initWithTrack:[audioAssetTracks objectAtIndex:0] outputSettings:outputSettings];
[assetReader addOutput:assetReaderTrackOutput];

AVAssetReaderAudioMixOutput *assetReaderAudioMixOutput = [AVAssetReaderAudioMixOutput assetReaderAudioMixOutputWithAudioTracks:audioAssetTracks audioSettings:outputSettings];
[assetReader addOutput:assetReaderAudioMixOutput];

AVAssetReaderVideoCompositionOutput *assetReaderVideoCompositionOutput = [AVAssetReaderVideoCompositionOutput assetReaderVideoCompositionOutputWithVideoTracks:audioAssetTracks videoSettings:outputSettings];
[assetReader addOutput:assetReaderVideoCompositionOutput];

[assetReader startReading];

CMSampleBufferRef buffer;
while ( assetReader.status==AVAssetReaderStatusReading ){
	buffer = [assetReaderTrackOutput copyNextSampleBuffer];
}
```


### Mix

Mix sounds :
	AVMutableComposition + AVAssetExportSession : 44.1k m4a	AVAssetReader + AVAssetWriter : setup sample,channels,rate.
	
```objectivec

// Mix Composition
AVMutableComposition* mixComposition = [AVMutableComposition composition];
	// Composition can insert AVAsset, manage ALL tracks


// Audio Assets
NSURL *audio = [[NSURL alloc] initFileURLWithPath:[[NSBundle mainBundle] pathForResource:@"audio" ofType:@"wav"]];
AVURLAsset *audioAsset = [[AVURLAsset alloc]initWithURL:audio options:nil];
NSArray *audioAssetTracks = [audioAsset tracksWithMediaType:AVMediaTypeAudio];

// Mix Composition Audio Tracks
AVMutableCompositionTrack *audioTrack = [mixComposition addMutableTrackWithMediaType:AVMediaTypeAudio preferredTrackID:kCMPersistentTrackID_Invalid];

// Audio Track add various AVAssets' tracks
[audioTrack insertTimeRange:CMTimeRangeMake(kCMTimeZero, audioAsset.duration) ofTrack:[audioAssetTracks objectAtIndex:0] atTime:kCMTimeZero error:&error];

// AudioMix Parameters
AVMutableAudioMix *audioMix =[AVMutableAudioMix audioMix];
AVMutableAudioMixInputParameters *audioMixParameters = [AVMutableAudioMixInputParameters audioMixInputParameters];
[audioMixParameters setVolume:0.8f atTime:kCMTimeZero];
[audioMix setInputParameters:[NSArray arrayWithObject:audioMixParameters]];


// video assets
NSURL *video = [[NSURL alloc] initFileURLWithPath:[[NSBundle mainBundle] pathForResource:@"video" ofType:@"mp4"]];
AVURLAsset *videoAsset = [[AVURLAsset alloc] initWithURL:video options:nil];
NSArray *videoAssetTracks = [videoAsset tracksWithMediaType:AVMediaTypeVideo];
    
// Mix Composition Video Tracks
AVMutableCompositionTrack *videoTrack = [mixComposition addMutableTrackWithMediaType:AVMediaTypeVideo preferredTrackID:kCMPersistentTrackID_Invalid];

// Video Track add various AVAssets' tracks
[videoTrack insertTimeRange:CMTimeRangeMake(kCMTimeZero, videoAsset.duration) ofTrack:[videoAssetTracks objectAtIndex:0] atTime:kCMTimeZero error:nil];
[videoTrack setPreferredTransform:[[videoAssetTracks objectAtIndex:0] preferredTransform]];
    
// AVVideoComposition setting
AVMutableVideoComposition *videoComposition = [AVMutableVideoComposition videoComposition];
[videoComposition setFrameDuration:mixComposition.duration];
[videoComposition setRenderSize:mixComposition.naturalSize];
[videoComposition setRenderScale:1.0f];
// add other layers to the video
//[videoComposition setAnimationTool:[AVVideoCompositionCoreAnimationTool videoCompositionCoreAnimationToolWithPostProcessingAsVideoLayer:[CALayer layer] inLayer:[CALayer layer]]];
    
// every AssetTrack could have a LayerInstruction
AVMutableVideoCompositionLayerInstruction *layerInstruction = [AVMutableVideoCompositionLayerInstruction videoCompositionLayerInstructionWithAssetTrack:[videoAssetTracks objectAtIndex:0]];
// video Compostion could have many Instruction
AVMutableVideoCompositionInstruction *videoCompositionInstruction = [AVMutableVideoCompositionInstruction videoCompositionInstruction];
[videoCompositionInstruction setTimeRange:CMTimeRangeMake(kCMTimeZero, mixComposition.duration)];
[videoCompositionInstruction setLayerInstructions:[NSArray arrayWithObject:layerInstruction]];
[videoComposition setInstructions:[NSArray arrayWithObject:videoCompositionInstruction]];

// Configuration to Export Mix Composition
NSString *outputFilePath = [NSTemporaryDirectory() stringByAppendingPathComponent:[@"exportOutput" stringByAppendingPathExtension:@"m4a"]];
if ([[NSFileManager defaultManager] fileExistsAtPath:outputFilePath])
	[[NSFileManager defaultManager] removeItemAtPath:outputFilePath error:&error];

AVAssetExportSession* assetExport = [[AVAssetExportSession alloc] initWithAsset:mixComposition presetName:AVAssetExportPresetAppleM4A];
/*	H.264 and AAC
	AVAssetExportPresetLowQuality
	AVAssetExportPresetMediumQuality
	AVAssetExportPresetHighestQuality;
	AVAssetExportPresetAppleM4A;
	AVAssetExportPresetPassthrough;
	AVAssetExportPreset640x480
	AVAssetExportPreset960x540
	AVAssetExportPreset1280x720
	AVAssetExportPreset1920x1080
	AVAssetExportPreset3840x2160;
	*/
[assetExport setOutputURL:[NSURL fileURLWithPath:outputFilePath]];[assetExport setOutputFileType:AVFileTypeAppleM4A];
/*	AVFileTypeMPEGLayer3;
	AVFileTypeMPEG4;
	AVFileTypeAppleM4A;
	AVFileTypeQuickTimeMovie;
	AVFileTypeWAVE;
	*/

[assetExport setVideoComposition:videoComposition];=
[assetExport setAudioMix:audioMix];

[assetExport exportAsynchronouslyWithCompletionHandler:^{
   int exportStatus = assetExport.status;
   switch (exportStatus) {
       case AVAssetExportSessionStatusFailed:{
           NSError *exportError = assetExport.error;
           NSLog (@"AVAssetExportSessionStatusFailed: %@", exportError);
           break;
       }
       
       case AVAssetExportSessionStatusCompleted:NSLog(@"Completed");break;
       case AVAssetExportSessionStatusUnknown:NSLog(@"Unknown");break;
       case AVAssetExportSessionStatusExporting:NSLog(@"Exporting");break;
       case AVAssetExportSessionStatusCancelled:NSLog(@"Cancelled");break;
       case AVAssetExportSessionStatusWaiting:NSLog(@"Waiting");break;
       default:NSLog(@"didn't get export status");break;
   }
}];
```
	
---

## Break AVFoundation Down

There are two facets to the AV Foundation framework—APIs related to video and APIs related just to audio.

### Basic Units

The primary class that the AV Foundation framework uses to represent media is AVAsset.

	|- AVAsset - AVURLAsset (timed audiovisual media) 
		|- AVAssetResourceLoader 
		|	|- <AVAssetResourceLoaderDelegate>
		|	|- AVAssetResourceLoadingRequest 
		|		|- AVAssetResourceRenewalRequest
		|		|- AVAssetResourceLoadingContentInformationRequest
		|		|- AVAssetResourceLoadingDataRequest
		|
		|- AVComposition - AVMutableComposition (combines multiple file-based media in a custom temporal arrangement)
		|	|- AVCompositionTrack - AVMutableCompositionTrack 
		|		|- AVCompositionTrackSegment
		|
		|- AVAssetTrackGroup
		|	|- AVAssetTrack (the track-level inspection)
		|		|- AVAssetTrackSegment
		|
		|- AVMetadataItem - AVMutableMetadataItem
			|- AVMetadataItemFilter 


### AVCapture

	|- AVCaptureDevice (a physical capture device)
		|- AVCaptureDeviceFormat
		|	|- media types(720p), frame rates(60fps)
		|	|- AVFrameRateRange
		|
		|- AVCaptureSession
		|	|- sessionPreset
		|	|
		|	|- AVCaptureInput
		|	|	|- AVCaptureDeviceInput
		|	|	|	|- AVCaptureInputPort
		|	|	|	|- AVCaptureScreenInput
		|	|	|
		|	|	|- AVAudioSession
		|	|		|- AVAudioSessionCategory
		|	|		|- AVAudioSessionMode
		|	|		|- settings
		|	|		|- <AVAudioSessionDelegate>
		|	|		|- AVAudioSessionRouteDescription
		|	|			|- AVAudioSessionPortDescription
		|	|			|- AVAudioSessionChannelDescription
		|	|			|- AVAudioSessionDataSourceDescription
		|	|
		|	|- AVCaptureOutput
		|	|	|- AVCaptureStillImageOutput
		|	|	|
		|	|	|- AVCaptureAudioDataOutput
		|	|	|	|- <AVCaptureAudioDataOutputSampleBufferDelegate>
		|	|	|- AVCaptureAudioPreviewOutput
		|	|	|
		|	|	|- AVCaptureVideoDataOutput
		|	|	|	|- <AVCaptureVideoDataOutputSampleBufferDelegate>
		|	|	|
		|	|	|- AVCaptureFileOutput - AVCaptureMovieFileOutput
		|	|		|- H.264 and AAC
		|	|		|- <AVCaptureFileOutputRecordingDelegate>
		|	|
		|	|- AVCaptureConnection
		|	|	|- AVCaptureVideoOrientation
		|	|	|
		|	|	|- AVCaptureAudioChannel (the average and peak power levels)
		|	|
		|	|- AVCaptureVideoPreviewLayer
		|		|- videoGravity
		
		|- AVAudioPlayer
		|	|- <AVAudioPlayerDelegate>
		|
		|- AVAudioRecorder
			|- settings
			|- <AVAudioRecorderDelegate>
	
		|- AVMetadataObject 
			|- AVMetadataFaceObject
			|- AVMetadataMachineReadableCodeObject


### Assets Library and Writer, Reader

	ALAssetsLibrary (provides access to the videos and photos)
		|- ALAssetsGroup
			|- ALAssetsGroupType
			|- ALAssetsFilter
			|
			|- ALAsset (represents a photo or a video)
				|- ALAssetRepresentation

	|- AVAssetWriter 
		|- AVMediaSelectionGroup 
		|	|- AVMediaSelectionOption
		|		|- AVAssetWriterInputGroup 
		|			|- AVAssetWriterInput [CMSampleBufferRef]
		|				|- transform
		|
		|- AVAssetWriterInputMetadataAdaptor 
		|	|- AVTimedMetadataGroup - AVMutableTimedMetadataGroup
		|
		|- AVAssetWriterInputPixelBufferAdaptor [CVPixelBufferRef]
		|
		|- If multi threads are used, we should set markAsFinished, or we just finishWriting.

	|- AVAssetReader
		|- AVAssetReaderOutput
			|- AVAssetReaderAudioMixOutput
			|- AVAssetReaderVideoCompositionOutput
			|- AVAssetReaderSampleReferenceOutput
			|- AVAssetReaderTrackOutput
				|- AVAssetReaderOutputMetadataAdaptor 
					|- AVTimedMetadataGroup - AVMutableTimedMetadataGroup


### Mix

	|- AVAssetExportSession
		|- AVMutableComposition
		|	|- AVMutableCompositionTrack
		|		|- Track ID : kCMPersistentTrackID_Invalid
		|		|
		|		|- AVAssetTrack
		|
		|- AVAudioMix - AVMutableAudioMix 
		|	|- AVAudioMixInputParameters - AVMutableAudioMixInputParameters 
		|
		|- AVVideoComposition - AVMutableVideoComposition 
			|- AVVideoCompositionInstruction - AVMutableVideoCompositionInstruction 
			|	|- AVVideoCompositionLayerInstruction - AVMutableVideoCompositionLayerInstruction (transform)
			|
			|- AVVideoCompositionCoreAnimationTool





	|- AVVideoCompositionRenderContext
		
	|- AVAudioEngine
	|- AVAudioNode
	|	|- AVAudioIONode
	|	|	|- AVAudioInputNode
	|	|	|- AVAudioOutputNode
	|	|- AVAudioEnvironmentNode 
	|	|- AVAudioMixerNode
	|	|- AVAudioPlayerNode
	|	|	|- AVAudioFile
	|	|		|- AVAudioFormat
	|	|		|- AVAudioBuffer - AVAudioPCMBuffer
	|	|			|- AVAudioChannelLayout 
	|	|
	|	|- AVAudioUnit
	|		|- AVAudioUnitEffect 
	|		|	|- AVAudioUnitDelay 
	|		|	|- AVAudioUnitDistortion 
	|		|	|- AVAudioUnitEQ 
	|		|		|- AVAudioUnitEQFilterParameters 
	|		|	|- AVAudioUnitReverb
	|		|
	|		|- AVAudioUnitTimeEffect
	|		|	|- AVAudioUnitTimePitch 
	|		|	|- AVAudioUnitVarispeed 
	|		|
	|		|- AVAudioUnitGenerator
	|		
	|- AVAudioTime
	


### Player

	|- AVPlayer - AVQueuePlayer (manage playback of an asset)
	|	|- AVPlayerItem : AVAsset, AVURLAsset (manage an asset as a whole)
	|		|- AVPlayerItemStatus : 
	|		|		AVPlayerItemStatusUnknown
	|		|		AVPlayerItemStatusReadyToPlay
	|		|		AVPlayerItemStatusFailed
	|		|
	|		|- AVPlayerItemTrack : AVAssetTrack (manage the presentation state of an individual track)
	|		|
	|		|- AVPlayerItemAccessLog (retrieve the access log)
	|		|	|- AVPlayerItemAccessLogEvent (the access log item)
	|		|- AVPlayerItemErrorLog (retrieve the error log)
	|			|- AVPlayerItemErrorLogEvent (the error log item)
	|
	|- Managing
	|- AVPlayerMediaSelectionCriteria
	|
	|- AVPlayerLayer 

---

## Core Media

	CMTime
		|<- CMTimeMake
		|<- CMTimeMakeWithSeconds
		|
		|- CMTimeCompare
		|- CMTimeSubtract
		|- CMTimeShow
		
	CMTimeRange (a start time and duration)
		|<- CMTimeRangeMake
		|<- CMTimeRangeFromTimeToTime
		|
		|- CMTimeRangeContainsTime
		|- CMTimeRangeEqual
		|- CMTimeRangeContainsTimeRange
		|- CMTimeRangeGetUnion
		
	CMSampleBuffer (video data represented by Core Media)
		|- CMSampleBufferRef
		|	|- CMBlockBuffer (data: one or more media samples)
		|	|- CVImageBuffer (reference: frame pixel buffer defined by Core Video)
		
	CMFormatDescription
		|- CMFormatDescriptionRef
		|	|- CMVideoFormatDescriptionGetCodecType
		|	|- CMVideoFormatDescriptionGetDimensions
		
		
	CMTime 是一个用来描述视频时间的结构体
	* CMTimeMake(a,b) a当前第几帧,b每秒钟多少帧. 当前播放时间a/b.
	* CMTimeMakeWithSeconds(a,b) a当前时间,b每秒钟多少帧.

	CMTimeMake
		int64_t value = 10000;
		int32_t preferredTimeScale = 600;
		CMTime inTime = CMTimeMake(value, preferredTimeScale);
		CMTimeShow(inTime);
	OUTPUT: {10000/600 = 16.667} 代表时间为16.667s, 视频一共1000帧，每秒600帧

	CMTimeMakeWithSeconds
		Float64 seconds = 5;
		int32_t preferredTimeScale = 600;
		CMTime inTime = CMTimeMakeWithSeconds(seconds, preferredTimeScale);
		CMTimeShow(inTime);
	OUTPUT: {3000/600 = 5.000} 代表当前时间为5s，视频一共有3000帧，一秒钟600帧
		
## Core Video

	manipulate individual video frames :
	* Color correction or other filtering	* Physical transforms of the video images
	* Adding video to an OpenGL scene
	* Adding additional information to frames
	* Compositing multiple video streams


	CVImageBuffer - CVImageBufferRef (pixels or OpenGL buffers)
	|- CVPixelBuffer - CVPixelBufferRef (holds pixels)
	
	CVBuffer - CVBufferRef (interact with buffers of data)
	
	CVPixelBufferPool - CVPixelBufferPoolRef (managing pixel buffers for repeated use)
		
	CVMetalTexture - CVMetalTextureRef (Metal framework)
	CVOpenGLESTexture - CVOpenGLESTextureRef (OpenGLES texture-based)
	
	Frame's Attachment:
		* Clean aperture, the cropping that is actually used when displaying.
		* Color space, the model used to represent an image, such as RGB or YCbCr.
		* Timestamps, when a particular frame appears in a movie.


## Media Player (Deprecated)

	|- MPMusicPlayerController
		|- MPMediaLibrary (iPod Library)
			|- MPMediaQuery
				|- MPMediaQuerySection
					|- MPMediaPredicate - MPMediaPropertyPredicate
						|- MPMediaEntity 
							|- MPMediaItemCollection
								|- MPMediaPlaylist
									|- MPMediaItem
										|- MPContentItem
										|- MPMediaPlaylist
					
	|- MPNowPlayingInfoCenter
	|- MPPlayableContentManager
	|- MPRemoteCommandCenter
		|- MPRemoteCommand 
		|	|- MPChangePlaybackRateCommand 
		|	|- MPFeedbackCommand 
		|	|- MPRatingCommand 
		|	|- MPSkipIntervalCommand 
		|- MPRemoteCommandEvent
			|- MPChangePlaybackRateCommandEvent 
			|- MPFeedbackCommandEvent 
			|- MPRatingCommandEvent 
			|- MPSkipIntervalCommandEvent
			|- MPSeekCommandEvent 

	|- MPMoviePlayerController
		|- MPTimedMetadata
		|
		|- MPMovieAccessLog
		|	|- MPMovieAccessLogEvent
		|- MPMovieErrorLog
			|- MPMovieErrorLogEvent
	
	|- MPMediaPickerController (select meida items)
	|- MPVolumeView (volume slider control)
	
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
AVPlayerItem *playerItem = [AVPlayerItem playerItemWithURL:[NSURL fileURLWithPath:[[NSBundle mainBundle] pathForResource:@"w5video" ofType:@"mov"]]];
//    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(itemDidFinishPlaying:) name:AVPlayerItemDidPlayToEndTimeNotification object:playerItem];
AVPlayer *player = [[AVPlayer alloc] initWithPlayerItem:playerItem];
AVPlayerViewController *playerViewController = [AVPlayerViewController new];
playerViewController.player = player;
[playerViewController.player play];//Used to Play On start
[self presentViewController:playerViewController animated:YES completion:nil];
```
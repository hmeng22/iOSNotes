# UIImagePickerController

	<UIImagePickerControllerDelegate, UINavigationControllerDelegate >
	MobileCoreServices.framework
	#import <MobileCoreServices/MobileCoreServices.h>
	
	1. self.imagePikcer.delegate = self;
	2. isSourceTypeAvailabe:availableMediaTypesForSourceType
		* UIImagePickerControllerSourceTypePhotoLibrary
		* UIImagePickerControllerSourceTypeCamera
		* UIImagePickerControllerSourceTypeSavedPhotosAlbum
	
	– imagePickerController:didFinishPickingMediaWithInfo:  
	– imagePickerControllerDidCancel:

	MediaInfo:
		UIImagePickerControllerMediaType ;指定用户选择的媒体类型（文章最后进行扩展）
		UIImagePickerControllerOriginalImage ;原始图片
		UIImagePickerControllerEditedImage ;修改后的图片
		UIImagePickerControllerCropRect ;裁剪尺寸
		UIImagePickerControllerMediaURL ;媒体的URL
		UIImagePickerControllerReferenceURL ;原件的URL
		UIImagePickerControllerMediaMetadata;当来数据来源是照相机的时候这个值才有效
	
	KUTTypeImage
		kUTTypeImage ;抽象的图片类型
		kUTTypeJPEG
		kUTTypeJPEG2000
		kUTTypeTIFF
		kUTTypePICT
		kUTTypeGIF
		kUTTypePNG
		kUTTypeQuickTimeImage
		kUTTypeAppleICNS
		kUTTypeBMP
		kUTTypeICO
		
	KUTTypeMovie
		kUTTypeAudiovisualContent ;抽象的声音视频
		kUTTypeMovie ;抽象的媒体格式（声音和视频）
		kUTTypeVideo ;只有视频没有声音
		kUTTypeAudio ;只有声音没有视频
		kUTTypeQuickTimeMovie
		kUTTypeMPEG
		kUTTypeMPEG4
		kUTTypeMP3
		kUTTypeMPEG4Audio
		kUTTypeAppleProtectedMPEG4Audio

```objectivec
<UIImagePickerControllerDelegate, UINavigationControllerDelegate > 
@property (nonatomic) UIImagePickerController *picker;

self.picker = [[UIImagePickerController alloc] init];

if ([UIImagePickerController isSourceTypeAvailable:UIImagePickerControllerSourceTypeCamera]) {
        NSArray *mediaTypers = [UIImagePickerController availableMediaTypesForSourceType:UIImagePickerControllerSourceTypeCamera];
        if ([mediaTypers containsObject:(NSString *)kUTTypeImage]) {
            self.picker.delegate = self;
            self.picker.sourceType = UIImagePickerControllerSourceTypeCamera;
            self.picker.mediaTypes = mediaTypers;
            self.picker.allowsEditing = NO;
            self.picker.modalPresentationStyle = UIModalPresentationCurrentContext;
            self.picker.cameraDevice = UIImagePickerControllerCameraDeviceRear;
            self.picker.cameraFlashMode = UIImagePickerControllerCameraFlashModeAuto;
            /* The user wants to use the camera interface. Set up our custom overlay view for the camera.*/
            self.picker.showsCameraControls = NO;
            self.picker.navigationBarHidden = YES;
            self.picker.cameraOverlayView = self.cameraView;
           
            [self presentViewController:self.picker animated:YES completion:nil];
        }
}

[self.picker takePicture];


if ([UIImagePickerController isSourceTypeAvailable:UIImagePickerControllerSourceTypePhotoLibrary]) {
   NSArray *mediaTypers = [UIImagePickerController availableMediaTypesForSourceType:UIImagePickerControllerSourceTypePhotoLibrary];
   if ([mediaTypers containsObject:(NSString *)kUTTypeImage]) {
       self.picker.delegate = self;
       self.picker.sourceType = UIImagePickerControllerSourceTypePhotoLibrary;
       self.picker.mediaTypes = mediaTypers;
       
       [self presentViewController:self.picker animated:YES completion:nil];
   }
}


// This method is called when an image has been chosen from the library or taken from the camera.
- (void)imagePickerController:(UIImagePickerController *)picker didFinishPickingMediaWithInfo:(NSDictionary *)info
{    
     if ([[info objectForKey:UIImagePickerControllerMediaType] isEqualToString:(NSString*)kUTTypeImage]) {
			UIImage* image = [info objectForKey:UIImagePickerControllerOriginalImage];
			UIImageWriteToSavedPhotosAlbum(image, self, nil, nil);
		} else if ([[info objectForKey:UIImagePickerControllerMediaType] isEqualToString:(NSString*)kUTTypeMovie]) {
			NSString* path = ［info objectForKey:UIImagePickerControllerMediaURL] path];
			UISaveVideoAtPathToSavedPhotosAlbum(path, self, nil, nil);
		}
    [picker dismissViewControllerAnimated:YES completion:nil];
}

- (void)imagePickerControllerDidCancel:(UIImagePickerController *)picker
{
    [self dismissViewControllerAnimated:YES completion:NULL];
}

- (void)flashButtonAction:(UIButton *)sender
{
    if (self.picker.cameraFlashMode == UIImagePickerControllerCameraFlashModeAuto) {
        self.picker.cameraFlashMode = UIImagePickerControllerCameraFlashModeOn;
    }else if(self.picker.cameraFlashMode == UIImagePickerControllerCameraFlashModeOn){
        self.picker.cameraFlashMode = UIImagePickerControllerCameraFlashModeOff;
    }else{
        self.picker.cameraFlashMode = UIImagePickerControllerCameraFlashModeAuto;
    }
}

- (void)frontRearButtonAction:(UIButton *)sender
{
    if (self.picker.cameraDevice == UIImagePickerControllerCameraDeviceRear ) {
        self.picker.cameraDevice = UIImagePickerControllerCameraDeviceFront;
    }else {
        self.picker.cameraDevice = UIImagePickerControllerCameraDeviceRear;
    }
}
```

Simple Image Processing

```objectivec
UIImage *image;
CGSize newSize (100,100);
UIGraphicsBeginImageContext(newSize);
[image drawInRect:CGRectMake(0,0,newSize.width,newSize.height);
UIImage *newImage = UIGraphicsGetImageFromCurrentImageContext();
UIGraphicsEndImageContext();
```

Save To SandBox

```objectivec
// 保存图片到document
NSData *imageData = UIImagePNGRepresentation(newImage);
NSArray *paths = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);
NSString *documentsDirectory = [paths objectAtIndex:0];
NSString *fullPathToFile = [documentsDirectory stringByAppendingPathComponent:newImageName];
[imageData writeToFile:fullPathToFile atomically:NO];

// 从文档目录下获取Documents路径
[NSHomeDirectory() stringAppendingPathComponent:@"Documents"];

```
## Scan QRcode

```objectivec
<AVCaptureMetadataOutputObjectsDelegate>

@property (nonatomic, strong) AVCaptureDevice *device;
@property (nonatomic, strong) AVCaptureDeviceInput *input;
@property (nonatomic, strong) AVCaptureSession *session;
@property (nonatomic, strong) AVCaptureMetadataOutput *output;

@property (nonatomic, strong) AVCaptureVideoPreviewLayer *preview;

@property (nonatomic, strong) AVAudioPlayer *audioPlayer;

@property (nonatomic, strong) NSString *codeString;


self.device = [AVCaptureDevice defaultDeviceWithMediaType:AVMediaTypeVideo];
    
self.input = [AVCaptureDeviceInput deviceInputWithDevice:self.device error:nil];
    
self.session = [[AVCaptureSession alloc] init];
    
self.output = [[AVCaptureMetadataOutput alloc] init];
[self.session addOutput:self.output];
[self.session addInput:self.input];
    
[self.output setMetadataObjectsDelegate:self queue:dispatch_get_main_queue()];
self.output.metadataObjectTypes = @[AVMetadataObjectTypeQRCode];
    
self.preview = [AVCaptureVideoPreviewLayer layerWithSession:self.session];
self.preview.videoGravity = AVLayerVideoGravityResizeAspectFill;
self.preview.frame = CGRectMake(0, 0, self.view.frame.size.width, self.view.frame.size.height);
    
AVCaptureConnection *con = self.preview.connection;
    
con.videoOrientation = AVCaptureVideoOrientationPortrait;
    
[self.view.layer insertSublayer:self.preview atIndex:0];
    
    
NSString *beepFilePath = [[NSBundle mainBundle] pathForResource:@"beep" ofType:@"mp3"];
NSURL *beepURL = [NSURL URLWithString:beepFilePath];
NSError *error;
    
self.audioPlayer = [[AVAudioPlayer alloc] initWithContentsOfURL:beepURL error:&error];
if (error) {
    NSLog(@"Could not play beep file.");
    NSLog(@"%@", [error localizedDescription]);
}
else{
    [_audioPlayer prepareToPlay];
}
    
[self.session startRunning];



- (void)captureOutput:(AVCaptureOutput *)captureOutput didOutputMetadataObjects:(NSArray *)metadataObjects fromConnection:(AVCaptureConnection *)connection
{
    if (metadataObjects != nil && [metadataObjects count] > 0) {
        for (AVMetadataObject *metadata in metadataObjects) {
            if ([[metadata type] isEqualToString:AVMetadataObjectTypeQRCode]) {
                // Find a valid QR code.
                [self.session stopRunning];
                
                if (self.audioPlayer) {
                    [self.audioPlayer play];
                }
                
                NSLog(@"Metadata Object : %@ .",metadata);
                
                self.codeString = [(AVMetadataMachineReadableCodeObject *)metadata stringValue];
                
                [self performSegueWithIdentifier:@"showContent" sender:nil];
            }
        }
    }
}
```

## Generate QRcode

```objectivec
@property (weak, nonatomic) IBOutlet UIImageView *imageView;
@property (nonatomic, strong) CIImage *qrcodeImage;

NSData *textData = [self.textView.text dataUsingEncoding:NSISOLatin1StringEncoding];
CIFilter *filter = [CIFilter filterWithName:@"CIQRCodeGenerator"];
[filter setValue:textData forKey:@"inputMessage"];
[filter setValue:@"Q" forKey:@"inputCorrectionLevel"];
    
self.qrcodeImage = [filter outputImage];
    
float scaleX = self.imageView.frame.size.width / self.qrcodeImage.extent.size.width;
float scaleY = self.imageView.frame.size.height / self.qrcodeImage.extent.size.height;
    
CIImage *transformedImage = [self.qrcodeImage imageByApplyingTransform:CGAffineTransformMakeScale(scaleX, scaleY)];
    
[self.imageView setImage:[UIImage imageWithCIImage:transformedImage]];
```



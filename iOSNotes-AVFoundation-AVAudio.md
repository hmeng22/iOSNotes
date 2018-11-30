# AVFoundation - AVAudio

```objectivec
#import <AVFoundation/AVFoundation.h>
<AVAudioRecorderDelegate, AVAudioPlayerDelegate>

NSMutableDictionary *recordSetting = [NSMutableDictionary dictionary];
[recordSetting setValue:[NSNumber numberWithInt:kAudioFormatLinearPCM] forKey:AVFormatIDKey];
[recordSetting setValue:[NSNumber numberWithFloat:44100.0] forKey:AVSampleRateKey];
[recordSetting setValue:[NSNumber numberWithInt:2] forKey:AVNumberOfChannelsKey];
[recordSetting setValue:[NSNumber numberWithInt:16] forKey:AVLinearPCMBitDepthKey];
[recordSetting setValue:[NSNumber numberWithInt:AVAudioQualityMax] forKey:AVEncoderAudioQualityKey];
    
NSArray *pathComponents = [NSArray arrayWithObjects:[NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) lastObject], @"test.wav", nil];
NSURL *outputFileURL = [NSURL fileURLWithPathComponents:pathComponents];

NSLog(@"path : %@ .",outputFileURL);

AVAudioSession *audioSession = [AVAudioSession sharedInstance];
[audioSession setCategory:AVAudioSessionCategoryPlayAndRecord error:nil];
[audioSession setActive:YES error:nil];
    
NSError *error = nil;
self.audioRecorder = [[AVAudioRecorder alloc] initWithURL:outputFileURL settings:recordSetting error:&error];
self.audioRecorder.delegate = self;
    
if (error)
{
    NSLog(@"error: %@", [error localizedDescription]);
} else {
    [self.audioRecorder prepareToRecord];
    [self.audioRecorder record];
}

[self.audioRecorder stop];

- (void)audioRecorderDidFinishRecording:(AVAudioRecorder *)recorder successfully:(BOOL)flag {
    NSLog(@"Record Done . flag %d .",flag);
}

- (void)audioPlayerDidFinishPlaying:(AVAudioPlayer *)player successfully:(BOOL)flag {
    NSLog(@"Play Done . flah %d .", flag);
}
```
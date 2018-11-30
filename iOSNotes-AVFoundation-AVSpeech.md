## AVSpeechUtterance

The basic unit of speech synthesis, voice, pitch, rate, and delay.

```objectivec
AVSpeechUtterance *utterance = [AVSpeechUtterance speechUtteranceWithString:@"Hello World"];
[utterance setRate:AVSpeechUtteranceDefaultSpeechRate];
```

## AVSpeechSynthesizer <AVSpeechSynthesizerDelegate>

pause or stop.

```objectivec
AVSpeechSynthesizer *synthesizer;
[synthesizer speakUtterance:utterance];
```

## AVSpeechSynthesizerDelegate

* When speech pauses or resumes
* When the synthesizer starts or finishes speaking a block of text (as encapsulated by an AVSpeechUtterance object)
* As the synthesizer produces each individual unit of speech

For the third case, you can implement speechSynthesizer:willSpeakRangeOfSpeechString:utterance: to provide a user interface in which each word is visibly highlighted as it is spoken.

```objectivec
- speechSynthesizer:didCancelSpeechUtterance:
- speechSynthesizer:didContinueSpeechUtterance:
- speechSynthesizer:didFinishSpeechUtterance:
- speechSynthesizer:didPauseSpeechUtterance:
- speechSynthesizer:didStartSpeechUtterance:
- speechSynthesizer:willSpeakRangeOfSpeechString:utterance:
```
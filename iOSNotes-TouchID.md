## TouchID

```objectivec
@import LocalAuthentication;

LAContext *context = [[LAContext alloc] init];
NSError *error;

// check if the policy can be evaluated
if (![context canEvaluatePolicy:LAPolicyDeviceOwnerAuthenticationWithBiometrics error:&error])
{
    NSLog(@"error:%@", error);
    NSString *msg = [NSString stringWithFormat:@"Can't evaluate policy! %@", error.localizedDescription];
    [SVProgressHUD showErrorWithStatus:msg];
    return;
}

// evaluate
[context evaluatePolicy:LAPolicyDeviceOwnerAuthenticationWithBiometrics
        localizedReason:@"{Application reason for authentication}"
                  reply:
 ^(BOOL success, NSError *authenticationError) {
     
     dispatch_async(dispatch_get_main_queue(), ^{
         
         if (success) {
             [SVProgressHUD showSuccessWithStatus:@"SUCCEEDED!"];
         }
         else {
             NSLog(@"error:%@", authenticationError);
             [SVProgressHUD showErrorWithStatus:[NSString stringWithFormat:@"FAILED! %@", authenticationError.localizedDescription]];
         }
     });
 }];
```
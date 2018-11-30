## Bluetooth

A **peripheral** typically has data that is needed by other devices.  
A **central** typically uses the information served up by peripherals to accomplish some particular task.  
A **service** is a collection of data and associated behaviors for accomplishing a function or feature of a device.  
A **characteristic** provides further details about a peripheral’s service.  

CBCentralManager - CBPeripheral : CBService + CBCharacteristic  
CBPeripheralManager - CBCentral : CBMutableService and CBMutableCharacterstic

```objectivec
TransferService.h
#define TRANSFER_SERVICE_UUID           @"E20A39F4-73F5-4BC4-A12F-17D1AD07A961"
#define TRANSFER_CHARACTERISTIC_UUID    @"08590F7E-DB05-467E-8757-72F6FAEB13D4"

static NSString * const kServiceUUID = @"180";
```

```objectivec
自定义蓝牙类 要使用@property (strong) 引用, 防止ARC自动释放而导致不能调用delegate方法.

蓝牙模块存在自动断线情况, 在didDisconnectPeripheral方法中添加连接代码.

后台接收数据
点击.plist文件，在key是 Required background modes添加两个item，一个是App communication using CoreBluetooth另一个是App shares data using CoreBluetooth 
```

```objectivec
主模块 : 
CoreBluetooth.framework

#import <CoreBluetooth/CoreBluetooth.h>

<CBCentralManagerDelegate, CBPeripheralDelegate>

@property (strong, nonatomic) CBCentralManager *centralManager;
@property (strong, nonatomic) CBPeripheral *discoveredPeripheral;
@property (strong, nonatomic) NSMutableData *data;

self.centralManager = [[CBCentralManager alloc] initWithDelegate:self queue:dispatch_queue_create("CentralManagerQueue",DISPATCH_QUEUE_SERIAL)];

self.data = [[NSMutableData alloc] init];

--- --- --- --- --- --- --- --- --- --- --- --- 

/** centralManagerDidUpdateState is a required protocol method.
 *  Usually, you'd check for other states to make sure the current device supports LE, is powered on, etc.
 *  In this instance, we're just using it to wait for CBCentralManagerStatePoweredOn, which indicates
 *  the Central is ready to be used.
 */
- (void)centralManagerDidUpdateState:(CBCentralManager *)central {
    
    switch (central.state) {
        case CBCentralManagerStatePoweredOn:
            NSLog(@"CBCentralManager Powered On");
            [self.centralManager scanForPeripheralsWithServices:@[[CBUUID UUIDWithString:servicesUUID]] options:@{CBCentralManagerScanOptionAllowDuplicatesKey:@YES}];
            
            //30秒以后停止
    double delayInSeconds = 30.0;
    dispatch_time_t popTime = dispatch_time(DISPATCH_TIME_NOW, (int64_t)(delayInSeconds*NSEC_PER_SEC));
    dispatch_after(popTime, dispatch_get_main_queue(), ^(void){
        [_managerstopScan];//停止扫描
       NSLog(@"停止扫描。。");
    });
            
            break;
            
        case CBCentralManagerStatePoweredOff:
            NSLog(@"CBCentralManager Powered Off");
            [self.centralManager stopScan];
            break;
            
        case CBCentralManagerStateUnsupported:
            NSLog(@"CBCentralManager Un Supported");
            break;
            
        case CBCentralManagerStateResetting:
            NSLog(@"CBCentralManager Resetting");
            break;
            
        case CBCentralManagerStateUnauthorized:
            NSLog(@"CBCentralManager Unauthorized");
            break;
            
        case CBCentralManagerStateUnknown:
            NSLog(@"CBCentralManager Unknown");
            break;
            
        default:
            NSLog(@"CBCentralManager did change state");
            break;
    }
}

--- --- --- --- --- --- --- --- --- --- --- --- 

/** This callback comes whenever a peripheral that is advertising the TRANSFER_SERVICE_UUID is discovered.
 *  We check the RSSI, to make sure it's close enough that we're interested in it, and if it is, 
 *  we start the connection process
 */
- (void)centralManager:(CBCentralManager *)central didDiscoverPeripheral:(CBPeripheral *)peripheral advertisementData:(NSDictionary *)advertisementData RSSI:(NSNumber *)RSSI
{
	[self.centralManager stopScan];
 
 	NSlog(@"Discovered %@ at %@ .", peripheral.name, RSSI);
    
    if (self.discoveredPeripheral != peripheral) {
        self.discoveredPeripheral = peripheral;
        NSLog(@"Connecting to peripheral %@", peripheral);
        // Connects to the discovered peripheral
        [self.centralManager connectPeripheral:peripheral options:nil];
    }
}

/** We've connected to the peripheral, now we need to discover the services and characteristics to find the 'transfer' characteristic.
 */
- (void)centralManager:(CBCentralManager *)central didConnectPeripheral:(CBPeripheral *)peripheral
{
	// set Peripheral delegate
	peripheral.delegate = self;

 	// Clears the data that we may already have
   [self.data setLength:0];
   
   // Asks the peripheral to discover the service
   [peripheral discoverServices:@[ [CBUUID UUIDWithString:TRANSFER_SERVICE_UUID] ]];
}

/** If the connection fails for whatever reason, we need to deal with it.
 */
- (void)centralManager:(CBCentralManager *)central didFailToConnectPeripheral:(CBPeripheral *)peripheral error:(NSError *)error
{
	
}

--- --- --- --- --- --- --- --- --- --- --- --- 

/** The Transfer Service was discovered
 */
- (void)peripheral:(CBPeripheral *)peripheral didDiscoverServices:(NSError *)error
{
	if (error) {
        NSLog(@"Error discovering service: %@", [error localizedDescription]);
        return;
    }
    for (CBService *service in peripheral.services) {
        NSLog(@"Service found with UUID: %@", service.UUID);
        // Discovers the characteristics for a given service
        if ([service.UUID isEqual:[CBUUID UUIDWithString:TRANSFER_SERVICE_UUID]]) {
            [peripheral discoverCharacteristics:@[[CBUUID UUIDWithString:TRANSFER_CHARACTERISTIC_ONE_UUID]] forService:service];
        }
    }
}

/** The Transfer characteristic was discovered.
 *  Once this has been found, we want to subscribe to it, which lets the peripheral know we want the data it contains
 */
- (void)peripheral:(CBPeripheral *)peripheral didDiscoverCharacteristicsForService:(CBService *)service error:(NSError *)error
{
	if (error) {
        NSLog(@"Error discovering characteristic: %@", [error localizedDescription]);
        return;
    }
    
    if ([service.UUID isEqual:[CBUUID UUIDWithString:TRANSFER_SERVICE_UUID]]) {
        for (CBCharacteristic *characteristic in service.characteristics) {
            if ([characteristic.UUID isEqual:[CBUUID UUIDWithString:TRANSFER_CHARACTERISTIC_ONE_UUID]]) {
                [peripheral setNotifyValue:YES forCharacteristic:characteristic];
            }
            if ([characteristic.UUID isEqual:[CBUUID UUIDWithString:TRANSFER_CHARACTERISTIC_TWO_UUID]]) {
                [peripheral setNotifyValue:YES forCharacteristic:characteristic];
            }
            if ([characteristic.UUID isEqual:[CBUUID UUIDWithString:TRANSFER_CHARACTERISTIC_THREE_UUID]]) {
                [peripheral setNotifyValue:YES forCharacteristic:characteristic];
            }
        }
    }
}

--- --- --- --- --- --- --- --- --- --- --- --- 

/** The peripheral letting us know whether our subscribe/unsubscribe happened or not
 */
- (void)peripheral:(CBPeripheral *)peripheral didUpdateNotificationStateForCharacteristic:(CBCharacteristic *)characteristic error:(NSError *)error
{
	if (error) {
		NSLog(@"Error changing notification state: %@", error.localizedDescription);
    }
    // Exits if it's not the transfer characteristic
    if (![characteristic.UUID isEqual:[CBUUID UUIDWithString:TRANSFER_CHARACTERISTIC_ONE_UUID]]) {
    	return;
    }
    // Notification has started
    if (characteristic.isNotifying) {
    	NSLog(@"Notification began on %@", characteristic);
    	// reading
    	[peripheral readValueForCharacteristic:characteristic];
    	
    	// writing the value of a characteristic
    	[self.discoveredPeripheral writeValue:dataToWrite forCharacteristic:interestingCharacteristic type:CBCharacteristicWriteWithResponse];
    } else {
    	// Notification has stopped
    	// so disconnect from the peripheral
    	NSLog(@"Notification stopped on %@.  Disconnecting", characteristic);
    	[self.centralManager cancelPeripheralConnection:self.discoveredPeripheral];
    }
}

/** This callback lets us know more data has arrived via notification on the characteristic
 */
- (void)peripheral:(CBPeripheral *)peripheral didUpdateValueForCharacteristic:(CBCharacteristic *)characteristic error:(NSError *)error
{
	if (error) {
        NSLog(@"Error discovering characteristics: %@", [error localizedDescription]);
        return;
	}

	if ([characteristic.UUID isEqual:[CBUUID UUIDWithString:TRANSFER_CHARACTERISTIC_ONE_UUID]]) {
        if( (characteristic.value)  || !error )
        {
            NSData *readData = characteristic.value;
            NSString *readString = [[NSString alloc] initWithData:readData encoding:NSUTF8StringEncoding];
            NSLog(@"characteristic value : %@...", readString);
        }
    }
    
    if ([characteristic.UUID isEqual:[CBUUID UUIDWithString:TRANSFER_CHARACTERISTIC_TWO_UUID]]) {
        NSData *comData = [GET_INPUT dataUsingEncoding:NSUTF8StringEncoding];
        NSData* valData = [NSData dataWithBytes:(Byte *)[comData bytes] length:sizeof(comData)];
        [peripheral writeValue:valData forCharacteristic:characteristic type:CBCharacteristicWriteWithResponse];
    }
    
    if ([characteristic.UUID isEqual:[CBUUID UUIDWithString:TRANSFER_CHARACTERISTIC_THREE_UUID]]) {
        
    }
}

- (void) peripheral:(CBPeripheral *)peripheral didWriteValueForCharacteristic:(CBCharacteristic *)characteristic error:(NSError *)error
{
	
}
--- --- --- --- --- --- --- --- --- --- --- --- 

/** Once the disconnection happens, we need to clean up our local copy of the peripheral
 */
- (void)centralManager:(CBCentralManager *)central didDisconnectPeripheral:(CBPeripheral *)peripheral error:(NSError *)error
{
	self.discoveredPeripheral = nil;
}

- (void)cleanup
{
    // Don't do anything if we're not connected
    if (!self.discoveredPeripheral.isConnected) {
        return;
    }
    
    // See if we are subscribed to a characteristic on the peripheral
    if (self.discoveredPeripheral.services != nil) {
        for (CBService *service in self.discoveredPeripheral.services) {
            if (service.characteristics != nil) {
                for (CBCharacteristic *characteristic in service.characteristics) {
                    if ([characteristic.UUID isEqual:[CBUUID UUIDWithString:TRANSFER_CHARACTERISTIC_UUID]]) {
                        if (characteristic.isNotifying) {
                            // It is notifying, so unsubscribe
                            [self.discoveredPeripheral setNotifyValue:NO forCharacteristic:characteristic];
                            
                            // And we're done.
                            return;
                        }
                    }
                }
            }
        }
    }
    
    // If we've got this far, we're connected, but we're not subscribed, so we just disconnect
    [self.centralManager cancelPeripheralConnection:self.discoveredPeripheral];
}

--- --- --- --- --- --- --- --- --- --- --- --- 
```


```objectivec
从模块 : 
CoreBluetooth.framework

#import <CoreBluetooth/CoreBluetooth.h>

<CBPeripheralManagerDelegate, CBCentral>

@property (strong, nonatomic) CBPeripheralManager *peripheralManager;
@property (strong, nonatomic) CBMutableCharacteristic *transferCharacteristic;
@property (strong, nonatomic) CBMutableService *transferService;
@property (strong, nonatomic) NSData *dataToSend;
@property (nonatomic, readwrite) NSInteger sendDataIndex;

--- --- --- --- --- --- --- --- --- --- --- --- 

self.peripheralManager = [[CBPeripheralManager alloc] initWithDelegate:self queue:nil options:nil];

- (void)peripheralManagerDidUpdateState:(CBPeripheralManager *)peripheral
{}

--- --- --- --- --- --- --- --- --- --- --- --- 

self.transferCharacteristic = [[CBMutableCharacteristic alloc] initWithType:myCharacteristicUUID properties:CBCharacteristicPropertyRead value:myValue permissions:CBAttributePermissionsReadable];

self.transferService = [[CBMutableService alloc] initWithType:myServiceUUID primary:YES];
* A primary service describes the primary functionality of a device and can be included (referenced) by another service.
* A secondary service describes a service that is relevant only in the context of another service that has referenced it.

self.transferService.characteristics = @[self.transferCharacteristic];

// publishing services
[self.peripheralManager addService:myService];

- (void)peripheralManager:(CBPeripheralManager *)peripheral didAddService:(CBService *)service error:(NSError *)error
{
	if (error == nil) {
		// advertising services
		[self.peripheralManager startAdvertising:@{ CBAdvertisementDataServiceUUIDsKey : @[myFirstService.UUID, mySecondService.UUID] }];
	}
}

- (void)peripheralManagerDidStartAdvertising:(CBPeripheralManager *)peripheral error:(NSError *)error
{}

--- --- --- --- --- --- --- --- --- --- --- --- 

- (void)peripheralManager:(CBPeripheralManager *)peripheral didReceiveReadRequest:(CBATTRequest *)request
{}

// After set the value, respond to the remote central to indicate that the request was successfully fulfilled by calling
respondToRequest:withResult:
// Call the respondToRequest:withResult: method exactly once each time the peripheralManager:didReceiveReadRequest: delegate method is called

[self.peripheralManager respondToRequest:[requests objectAtIndex:0] withResult:CBATTErrorSuccess];

--- --- --- --- --- --- --- --- --- --- --- --- 

/** Catch when someone subscribes to our characteristic, then start sending them data
 */
- (void)peripheralManager:(CBPeripheralManager *)peripheral central:(CBCentral *)central didSubscribeToCharacteristic:(CBCharacteristic *)characteristic
{
	NSData *updatedValue = // fetch the characteristic's new value	BOOL didSendValue = [self.peripheralManager updateValue:updatedValue forCharacteristic:characteristic onSubscribedCentrals:nil];

}

/** Recognise when the central unsubscribes
 */
- (void)peripheralManager:(CBPeripheralManager *)peripheral central:(CBCentral *)central didUnsubscribeFromCharacteristic:(CBCharacteristic *)characteristic
{}

--- --- --- --- --- --- --- --- --- --- --- --- 

/** This callback comes in when the PeripheralManager is ready to send the next chunk of data.
 *  This is to ensure that packets will arrive in the order they are sent
 */
- (void)peripheralManagerIsReadyToUpdateSubscribers:(CBPeripheralManager *)peripheral
{}
```
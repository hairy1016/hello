<img src="http://www.serafim-tech.com/images/logo/20140831LOGO.png" width=200> 

# iKeyboConnect-objc
-
Objective c library for communicating between ios device and serafim iKeybo

## Background
Serafim iKeybo is the world's first "changable" laser projection keyboard. It has different projection mode include piano and coordinate detecting. This library support communicating between ios app and iKeybo. 

## Features
- coordinate mode support all ios in-build gesture recognizer
- change iKeybo mode in keyboard, piano and coordinate
- scan, connect by one method with self setting time interval
- other self define gesture

## Requirements
- IOS 8.0+ / OSX 10.11+
- Xcode 7.3+

## installation
Just copy the header file in 'include' directory and iKeyboConnect-objc.a file into your project

## usage
Below you find some examples of how the library can be used. Accompanied in the repository you find an example project that demonstrates a usage of the framework in practice.

#### common
Make sure to import the iKeyboConnect-obje.h in files that use it.

```javascript
#import "iKeyboConnect_objc.h"
```

#### iKcentral

Use iKcentralDelegate protocol handle different connection state.

```javascript
iKcentral* central = [[iKCentral alloc] init];
central.delegate = self;
```
Check bluetooth service is on
 
```javascript
-(void) centralManagerDidUpdateState:(centralState)state{
    switch (state) {
        case Unknown:
            break;
        case Unsupported:
            break;
        case Unauthorized:
            break;
        case Resetting:
            break;
        case PoweredOff:
            break;
        case PoweredOn:
            break;
        default:
            break;
    }
}
```

Auto scan, retrieve, connect with time interval

    -(void)connectIKeybo:(float)connectionTime;
    
Scan iKeybo device (only find unpaired device)

```javascript
-(void)startScanPeripheral;
-(void)startScanPeripheral:(float)scanTime;
-(void)stopScanPeripheral;
```

Retrieve the first paired and auto connected iKeybo

	-(iKPeripheral*)retrieveConnectedPeripheral;

Connect to an identical iKeybo

	//return false if the peripheral is not iKeybo
	-(BOOL)connectPeripheral:(iKPeripheral*)peripheral;
	-(void)connectIKeybo:(float)connectionTime;

#####  delegate: -(void) connectIKeyboTimeOut:(iKCentral*) central; will be called when connection timeout.

Other delegate protocol

```
//When scan method is called and find iKeybo
-(void) centralManager:(iKCentral*)central didDiscoverPeripheral:(iKPeripheral*)peripheral;
//When connect method is called and connect successfully
-(void) centralManager:(iKCentral*)central didConnectPeripheral:(iKPeripheral*)peripheral;
-(void) centralManager:(iKCentral*)central didFailToConnectPeripheral:(iKPeripheral*)peripheral error:(NSError*)error;
-(void) centralManager:(iKCentral*)central didDisconnectPeripheral:(iKPeripheral*)peripheral error:(NSError*)error;
```

#### iKPeripheral

Handle data communication. You can get the iKeybo peripheral when methods in iKCentralDelegate are called. Make the pointer point to it and set iKPeripheralDelegate.

```
//example
-(void) centralManager:(iKCentral*)central didConnectPeripheral:(iKPeripheral*)peripheral{
    self.peripheral = peripheral;
    self.peripheral.delegate = self;
}
```

After you set the delegate and peripheral did discover dharacteristics, you can change iKeybo mode and receive data.

```
//This will be called when we descover iKeybo service
-(void)peripheralDidDiscoverCharacteristicsForService:(iKPeripheral*)peripheral error:(NSError*)error;
```

Change iKeybo mode by this method

	-(void)changeiKeyboMode:(iKeyboMode) mode;
	//keyboard,piano,coordinate 

- In piano mode, data array size represent number of keys pushed with max size 3.  Each element is an NSNumber integer represent key numbers. When all fingers leave, data array will be [0,0,0]
- In coordinate mode, data array size represent number of finger with max size 3. Each element is an NSValue with CGPoint represent the coordinate. When all fingers leave, data array will be [].

```
-(void)peripheral:(iKPeripheral*)peripheral didUpdateValueForCharacteristic:(NSArray*)data error:(NSError*)error;
```

In iKPeripheral, there are 6 public boolean variance which control 6 gesture recognizer. You have to set these variance to be true to turn on the gesture recognizer then the delegate method will be called when gestures happen.

```
BOOL swipeGestureRecognizer;
BOOL panGestureRecognizer;
BOOL tapGestureRecognizer;
BOOL pinchGestureReconizer;
BOOL longPressGestureRecognizer;
BOOL rotationGestureRecognizer;
```

```
//iKPoint is just like CGPoint with x,y public float variance
//gestureState: touchBegin, touchChanged, touchEnded, touchCanceled
//up,down,right,left direction
-(void)gestureRecognizer:(iKPeripheral*)peripheral swipeGesture:(swipeDirection)direction;
-(void)gestureRecognizer:(iKPeripheral*)peripheral panGesture:(iKPoint*)vector state:(gestureState)state;
//Types: singleTap,doubleTap(tap twice quickly),doubleTouchTap(tap with two fingers),tripleTouchTap
-(void)gestureRecognizer:(iKPeripheral *)peripheral tapGesture:(iKPoint*)position tapType:(tapType)type state:(gestureState)state;
-(void)gestureRecognizer:(iKPeripheral *)peripheral pinchGesture:(iKPoint *)center scale:(float)scale state:(gestureState)state;
-(void)gestureRecognizer:(iKPeripheral *)peripheral longPressGesture:(iKPoint *)position;
-(void)gestureRecognizer:(iKPeripheral *)peripheral rotationGesture:(iKPoint *)center angle:(float)angle state:(gestureState)state;
```

## joeifj

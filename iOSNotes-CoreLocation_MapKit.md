# Core Location

	NSObject
		|- CLLocationManager
		|	|- CLHeading
		|	|- CLLocation
		|- CLRegion : abstract area that can be tracked
		|	|- CLBeaconRegion
		|	|- CLCircularRegion
		|- CLBeacon : the beacon that was encountered during region monitoring
		|- CLFloor : specifies the floor of the building
		|- CLGeocoder : convert coordinates
		|	|- CLPlacemark
		|- CLVisit
		|- <CLLocationManagerDelegate>

```objectivec
CoreLocation.framework
add this in info.plist
NSLocationAlwaysUsageDescription
NSLocationWhenInUseUsageDescription

#import <CoreLocation/CoreLocation.h>
<CLLocationManagerDelegate>

// Create the locationManager
CLLocationManager *locationManager = [[CLLocationManager alloc] init];
locationManager.delegate = self;
locationManager.pausesLocationUpdatesAutomatically = YES;
locationManager.activityType = CLActivityTypeAutomotiveNavigation;

locationManager.desiredAccuracy = KCLLocationAccuracyKilometer;
locationManager.distanceFilter = 500; // meters
[locationManager requestWhenInUseAuthorization];
[locationManager startUpdatingLocation];
[locagionManager stopUpdatingLocation];

// [locationManager startMonitoringSignificantLocationChanges];
// [locationManager stopMonitoringSignificantLocationChanges];

// CLLocationManagerDelegate methods
- (void)locationManager:(CLLocationManager *)manager didUpdateLocations:(NSArray *)locations {
	CLLocation *location = [locations lastObject];
	NSDate *eventDate = location.timestamp;
	NSTimeInterval howRecent = [eventDate timeIntervalSinceNow];
	if(abs(howRecent)<15.0) {
		NSLog(@"latitude %+.6f, longitude %+.6f\n", location.coordinate.latitude, location.coordinate.longitude);
	}
	
	// Defer updates until the user hikes a certain distance or when a certain amount of time has passed.
	if (!self.deferringUpdates) {
		CLLocationDistance distance = self.hike.goal - self.hike.distance;
		NSTimeInterval time = [self.nextAudible timeIntervalSinceNow];
		[locationManager allowDeferredLocationUpdatesUntilTraveled:distance timeout:time];
		self.deferringUpdates = YES;
	}
	
}

locationManagerDidPauseLocationUpdates:
locationManagerDidResumeLocationUpdates:

locationManager:didFinishDeferredUpdatesWithError:

locationManager:didChangeAuthorizationStatus:
```

## Region

```objectivec
// Creating and registering a geographical region based on a Map Kit overlay
- (void)registerRegionWithCircularOverlay:(MKCircle*)overlay andIdentifier:(NSString*)identifier {
   // If the overlay's radius is too large, registration fails automatically,
   // so clamp the radius to the max value.
   CLLocationDistance radius = overlay.radius;
   if (radius > self.locManager.maximumRegionMonitoringDistance) {
      radius = self.locManager.maximumRegionMonitoringDistance;
   }
 
   // Create the geographic region to be monitored.
   CLCircularRegion *geoRegion = [[CLCircularRegion alloc]
      initWithCenter:overlay.coordinate
              radius:radius
          identifier:identifier];
   [self.locManager startMonitoringForRegion:geoRegion];
}


// Creating and registering a beacon region
- (void)registerBeaconRegionWithUUID:(NSUUID *)proximityUUID andIdentifier:(NSString*)identifier {
   // Create the beacon region to be monitored.
   CLBeaconRegion *beaconRegion = [[CLBeaconRegion alloc]
      initWithProximityUUID:proximityUUID
                 identifier:identifier];
 
   // Register the beacon region with the location manager.
   [self.locManager startMonitoringForRegion:beaconRegion];
   
   // [self.locManager startRangingBeaconsInRegion:beaconRegion];
}

// Delegate method from the CLLocationManagerDelegate protocol.
- (void)locationManager:(CLLocationManager *)manager
        didRangeBeacons:(NSArray *)beacons
               inRegion:(CLBeaconRegion *)region {
 
   if ([beacons count] > 0) {
      CLBeacon *nearestExhibit = [beacons firstObject];
 
      // Present the exhibit-specific UI only when
      // the user is relatively close to the exhibit.
      if (CLProximityNear == nearestExhibit.proximity) {
         [self presentExhibitInfoWithMajorValue:nearestExhibit.major.integerValue];
      } else {
         [self dismissExhibitInfo];
   }
}

// notifyOnEntry = YES and notifyOnExit = YES
locationManager:didEnterRegion:
locationManager:didExitRegion:

locationManager:didRangeBeacons:inRegion:

locationManager:monitoringDidFailForRegion:withError:
// kCLErrorRegionMonitoringFailure
```

## Beacon

```objectivec
To use your iOS device as a beacon.

$ uuidgen

NSUUID *uuid = [[NSUUID alloc] initWithUUIDString:@""];
// Create the beacon region
CLBeaconRegion *beaconRegion = [[CLBeaconRegion alloc] initWithProximityUUID:uuid identifier:@"uuidIdentifier"];

NSDictionary *beaconPeripheralData = [beaconRegion peripheralDataWithMeasuredPower:nil];
// Create the peripheral manager.
CBPeripheralManager *peripheralManager = [[CBPeripheralManager alloc] initWithDelegate:selfqueue:nil options:nil];
 
// Start advertising your beacon's data.
[peripheralManager startAdvertising:beaconPeripheralData];
```

## Heading and Course

```objectivec
if([CLLocationManager headingAvailable]) {
	locationManager.headingFilter = 5;
	[locationManager startUpdatingHeading];
}

- (void)locationManager:(CLLocationManager *)manager didUpdateHeading:(CLHeading *)newHeading {
   if (newHeading.headingAccuracy < 0)
      return;
 
   // Use the true heading if it is valid.
   CLLocationDirection  theHeading = ((newHeading.trueHeading > 0) ?
            newHeading.trueHeading : newHeading.magneticHeading);
 
   self.currentHeading = theHeading;
   [self updateHeadingDisplays];
}
```

## Placemark

```objectivec
- (void)geocodeLocation:(CLLocation*)location forAnnotation:(MapLocation*)annotation
{
    if (!geocoder)
        geocoder = [[CLGeocoder alloc] init];
 
    [geocoder reverseGeocodeLocation:location completionHandler:
        ^(NSArray* placemarks, NSError* error){
            if ([placemarks count] > 0)
            {
                annotation.placemark = [placemarks objectAtIndex:0];
 
                // Add a More Info button to the annotation's view.
                MKPinAnnotationView* view = (MKPinAnnotationView*)[map viewForAnnotation:annotation];
                if (view && (view.rightCalloutAccessoryView == nil))
                {
                    view.canShowCallout = YES;
                    view.rightCalloutAccessoryView = [UIButton buttonWithType:UIButtonTypeDetailDisclosure];
                }
            }
    }];
}

// names into Coordinates 
[geocoder geocodeAddressString:@"1 Infinite Loop"
     completionHandler:^(NSArray* placemarks, NSError* error){
         for (CLPlacemark* aPlacemark in placemarks)
         {
             // Process the placemark.
         }
}];
```

## Visited Locations

```objectivec
[locationManager startMonitoringVisits];

locationMaanger:didVisit:
```


# Mapkit.framework

	|- MKMapView <MKMapViewDelegate>
		|- <MKAnnotation>
		|	|- MKAnnotationView
		|		|- MKPinAnnotationView
		|- MKUserLocation
				
		|- MKOverlayRenderer
		|	|- MKTileOverlayRenderer
		|	|- MKOverlayPathRender
		|	|	|- MKCircleRenderer
		|	|	|- MKPolygonRenderer
		|	|	|- MKPolylineRenderer
		|- MKTileOverlay
		|- MKShaper
			|- MKCircle
			|- MKMultiPoint
			|	|- MKPolygon
			|	|- MKPolyline
			|		|- MKGeodesicPolyline
			|- MKPointAnnotation
		
		|- MKOverlayView
			|- MKOverlayPathView
				|- MKCircleView
				|- MKPolygonView
				|- MKPolylineView
	
	|- MKMapItem
	
	|- MKDirections
		|- MKDirectionsRequest
		|- MKDirectionsResponse
			|- MKRoutes
			
	|- MKLocalSearch
		|- MKLocalSearchRequest
		|- MKLocalSearchResponse
	
	|- MKMapCamera
	
	|- MKMapSnapshot
		|- MKMapSnapshotOptions
		|- MKMapSnapshotter

## Basic

```objctivec
The Location : CLLocationCoordinate2D
The Region : MKCoordinateRegion
The Annotation : annotation -> callout -> accessory views
	protocol : MKAnnotation (details)
	subclass : MKAnnotationView (view)


<MKMapViewDelegate>

#define NY_LATITUDE 40.712784
#define NY_LONGITUDE -74.005941

CLLocationCoordinate2D location;
location.latitude = NY_LATITUDE;
location.longitude = NY_LONGITUDE;

MKMapView *mapView;
// MKMapTypeStandard,Satellite,Hybrid,SatelliteFlyover,HybridFlyover
mapView.mapType = MKMapTypeStandard;
mapView.rotateEnabled = YES;
mapView.zoomEnabled = YES;
mapView.scrollEnabled = YES;
mapView.pitchEnabled = YES;
mapView.showUserLocation = YES;

MKCoordinateRegion viewRegion = MKCoordinateRegionMakeWithDistance(location, 0.5*METERS_PER_MILE, 0.5*METERS_PER_MILE);
[mapView setRegion:viewRegion animated:YES];
MKCoordinateRegion viewRegion2;
viewRegion2.center.latitude = 37.786996;
viewRegion2.center.longitude = -122.440100;
viewRegion2.span.latitudeDelta = 0.2;
viewRegion2.span.longitudeDelta = 0.2;
[mapView setRegion:viewRegion2 animated:YES];

- (void)mapView:regionWillChangeAnimated:
- (void)mapView:regionDidChangeAnimated:
```
```objectivec
// Annotation : all customized
// 1. MKAnnotation data
@interface annotationData : NSObject <MKAnnotation>
@property (nonatomic, assign) CLLocationCoordinate2D coordinate;
@property (nonatomic, copy) NSString *title;
@property (nonatomic, copy) NSString *subtitle;

@property (nonatomic, assign) NSString *name;
@property (nonatomic, assign) NSURL *url;
@end

// 2. MKAnnotationView
@interface annotationView : MKAnnotationView
@property (nonatomic, assign) annotationData *data;
@end

- (instancetype)initWithAnnotation:(id<MKAnnotation>)annotation reuseIdentifier:(NSString *)reuseIdentifier {
    self = [super initWithAnnotation:annotation reuseIdentifier:reuseIdentifier];
    
    if (self) {
        self.opaque = NO;
    }
    
    return self;
}

- (void)drawRect:(CGRect)rect {
    [super drawRect:rect];
    
    CGContextRef ctx = UIGraphicsGetCurrentContext();
    CGContextSetLineWidth(ctx, 2.0);
    [[UIColor redColor] setFill];
    CGContextFillRect(ctx, CGRectMake(15, 15, 10, 10));
}

- (void)setSelected:(BOOL)selected animated:(BOOL)animated {
    NSLog(@"In Annotation selected.");
}

// 3. MKMapView
- (MKAnnotationView *)mapView:(MKMapView *)mapView viewForAnnotation:(id<MKAnnotation>)annotation {
    NSLog(@"view for annotation");
    if ([annotation isKindOfClass:[MKUserLocation class]]) {
        return nil;
    }
    
    if ([annotation isKindOfClass:[annotationView class]]) {
        annotationView *av = (annotationView *)[mapView dequeueReusableAnnotationViewWithIdentifier:@"mav"];
        if (!av) {
            av = [[annotationView alloc] initWithAnnotation:annotation reuseIdentifier:@"av"];
            av.canShowCallout = YES;
            av.draggable = YES;
            UIImageView *imageView = [[UIImageView alloc] initWithFrame:CGRectMake(0, 0, 46, 46)];
            imageView.image = [UIImage imageNamed:@"hello"];
            av.leftCalloutAccessoryView = imageView;
            UIButton *disclosureButton = [UIButton buttonWithType:UIButtonTypeDetailDisclosure];
            av.rightCalloutAccessoryView = disclosureButton;
        }
        
        av.annotation = annotation;
        
        return av;
    }
    return nil;
}

- (void)mapView:(MKMapView *)mapView didAddAnnotationViews:(NSArray<MKAnnotationView *> *)views {
    NSLog(@"did add annotationview");
}

- (void)mapView:(MKMapView *)mapView didSelectAnnotationView:(MKAnnotationView *)view {
    NSLog(@"select a annotationview");
}

- (void)mapView:(MKMapView *)mapView didDeselectAnnotationView:(MKAnnotationView *)view {
    NSLog(@"de-select a annotationview");
}

- (void)mapView:(MKMapView *)mapView annotationView:(MKAnnotationView *)view calloutAccessoryControlTapped:(UIControl *)control {
    NSLog(@"annotationview callout tapped");
}

- (void)mapView:(MKMapView *)mapView annotationView:(MKAnnotationView *)view didChangeDragState:(MKAnnotationViewDragState)newState fromOldState:(MKAnnotationViewDragState)oldState {
    NSLog(@"annotationview state changes");
}
```
```objectivec
// Overlay
// 1. Overlay data
CLLocationCoordinate2D  points[4];
points[0] = CLLocationCoordinate2DMake(41.000512, -109.050116);
points[1] = CLLocationCoordinate2DMake(41.002371, -102.052066);
points[2] = CLLocationCoordinate2DMake(36.993076, -102.041981);
points[3] = CLLocationCoordinate2DMake(36.99892, -109.045267);
MKPolygon* poly = [MKPolygon polygonWithCoordinates:points count:4];
poly.title = @"Colorado";

[mapView addOverlay:poly];

// 2. MKMapView
- (MKOverlayRenderer *)mapView:(MKMapView *)mapView rendererForOverlay:(id<MKOverlay>)overlay {
    NSLog(@"renderer for overlay");
    if ([overlay isKindOfClass:[MKPolygon class]])
    {
        MKPolygonRenderer*    aRenderer = [[MKPolygonRenderer alloc] initWithPolygon:(MKPolygon*)overlay];
        
        aRenderer.fillColor = [[UIColor cyanColor] colorWithAlphaComponent:0.2];
        aRenderer.strokeColor = [[UIColor blueColor] colorWithAlphaComponent:0.7];
        aRenderer.lineWidth = 3;
        
        return aRenderer;
    }
    
    return nil;
}

- (void)mapView:(MKMapView *)mapView didAddOverlayRenderers:(NSArray<MKOverlayRenderer *> *)renderers {
    NSLog(@"did add overlay renderers");
}
```

## MKMapSnapshotOptions

```objectivec
MKMapSnapshotOptions *options = [[MKMapSnapshotOptions alloc] init];
options.size = CGSizeMake(512, 512);
options.scale = [[UIScreen mainScreen] scale];
options.camera = self.mapView.camera;
options.mapType = MKMapTypeStandard;
    
MKMapSnapshotter *snapshotter = [[MKMapSnapshotter alloc] initWithOptions:options];
    
[snapshotter startWithCompletionHandler:^(MKMapSnapshot *snapshot, NSError *e) {
    if (e) {
        NSLog(@"error:%@", e);
    } else {
        self.imageView.image = snapshot.image;
    }
}];
```
# Google Maps & Places

## Google Maps

	pod init
	add pod 'GoogleMaps'
	pod install
	
	Info.plist
		LSApplicationQueriesSchemes : googlechromes and comgooglemaps
	
	Enable Google Services :
		Google Maps iOS, Google Plcaes iOS ...
		Google Plcaes Web Service

```objectivec
	GMSMapView <GMSMapViewDelegate>
		|- GMSCameraPosition
		|- GMSCameraUpdate
		
	GMSPanoramaView <GMSPanoramaViewDelegate>
		|- GMSPanoramaCamera
		|- GMSPanoramaService
			|- GMSPanorama
		
	GMSUISettings
		
	GMSMarker
		
	GMSMutablePath
		|- GMSPolyline
		|- GMSPolygon
		|- GMSCircle
			|- GMSStyleSpan
				|- GMSStrokeStyle
		
	GMSGroundOverlay
		|- 
```

### Basic Maps

```objectivec
AppDelegate.m
	@import GoogleMaps;
	application:didFinishLaunchingWithOptions:
		[GMSServices provideAPIKey:@"API_KEY"];

ViewController
	GMSCameraPosition *camera = [GMSCameraPosition cameraWithLatitude:1.285 longitude:103.848 zoom:12];
	GMSMapView *mapView = [GMSMapView mapWithFrame:CGRectZero camera:camera];
	// kGMSTypeNormal, Hybrid, Satellite, Terrain, None
	mapView.mapType = kGMSTypeHybrid;
	mapView.indoorEnabled = YES;
	mapView.myLocationEnabled = YES;
	mapView.trafficEnabled = YES;
	mapView.autoresizingMask = UIViewAutoresizingFlexibleWidth | UIViewAutoresizingFlexibleHeight | UIViewAutoresizingFlexibleTopMargin | UIViewAutoresizingFlexibleBottomMargin;
	// kGMSFrameRatePowerSave, Conservative, Maximum
	mapView.preferredFrameRate = kGMSFrameRatePowerSave;
	mapView.settings.compassButton = YES;
	mapView.settings.myLocationButton = YES;
	mapView.settings.scrollGestures = YES;
	mapView.settings.zoomGestures = YES;
	mapView.settings.tiltGestures = YES;
	mapView.settings.rotateGestures = YES;
	// Map padding
	UIEdgeInsets mapInsets = UIEdgeInsetsMake(500.0, 0.0, 0.0, 500.0);
	mapView.padding = mapInsets;
	mapView.delegate = self;
	self.view = mapView;
		
	// Marker
	// @property (nonatomic, copy) NSSet *markers;
	GMSMarker *marker = [[GMSMarker alloc] init];
	marker.position = CLLocationCoordinate2DMake(-33.86, 151.20);
	marker.title = @"Sydney";
	marker.snippet = @"Australia";
	marker.icon = [GMSMarker markerImageWithColor:[UIColor greenColor]];
	marker.appearAnimation = kGMSMarkerAnimationPop;
	marker.opacity = 0.6;
	marker.flat = YES;
	marker.draggable = YES;
	marker.groundAnchor = CGPointMake(0.5, 0.5);
	marker.rotation = 10;
	marker.infoWindowAnchor = CGPointMake(0.5, 0.5);
	marker.tracksInfoWindowChanges = YES;
	marker.map = self.gmsMapView;	// = nil;
	marker.panoramaView = self.gmsStreetView; // = nil;
	
	
	// Google Map URL Scheme
	if ([[UIApplication sharedApplication] canOpenURL:[NSURL URLWithString:@"comgooglemaps://"]]) {
        [[UIApplication sharedApplication] openURL:[NSURL URLWithString:@"comgooglemaps://?center=40.765819,-73.975866&zoom=14&views=traffic"]];
    } else {
        NSLog(@"Can't use comgooglemaps://");
    }
    Search : comgooglemaps://?q=Steamers+Lane+Santa+Cruz,+CA&center=37.782652,-122.410126&views=satellite,traffic&zoom=15
    Directions : comgooglemaps://?saddr=Google+Inc,+8th+Avenue,+New+York,+NY&daddr=John+F.+Kennedy+International+Airport,+Van+Wyck+Expressway,+Jamaica,+New+York&directionsmode=transit
    Callback : comgooglemaps-x-callback://?q=dessert&center=37.759748,-122.427135&x-success=sourceapp://?resume=true&x-source=Nom+Nom
    
    
	Delegate Methods : 
	- (void)mapView:(GMSMapView *)mapView didTapAtCoordinate:(CLLocationCoordinate2D)coordinate {
		NSLog(@"You tapped at %f,%f", coordinate.latitude, coordinate.longitude);
	}  
```
```objectivec
GMSMapView:
	[mapView moveCamera:[GMSCameraUpdate fitBounds:bounds withPadding:50.0f]];
	[mapView animateToLocation:CLLocationCoordinate2DMake(-33.868, 151.208)];
	mapView.camera = [GMSCameraPosition cameraWithTarget:CLLocationCoordinate2DMake(-33.868, 151.208); zoom:6];
	// 1:World,5:Landmass/Continent,10:City,15:Streets,20:Buildings
	[mapView animateToZoom:12];
	[mapView setMinZoom:10 maxZoom:mapView.maxZoom];
	// bearing : degrees clockwise from north
	[mapView animateToBearing:0];
	// angle : degrees from the nadir
	[mapView animateToViewingAngle:45];
	// remove markers
	[mapView clear];

GMSCameraPosition:
	CLLocationCoordinate2D vancouver = CLLocationCoordinate2DMake(49.26, -123.11);
	CLLocationCoordinate2D calgary = CLLocationCoordinate2DMake(51.05, -114.05);
	GMSCoordinateBounds *bounds = [[GMSCoordinateBounds alloc] initWithCoordinate:vancouver coordinate:calgary];
	GMSCameraPosition *camera = [mapView cameraForBounds:bounds insets:UIEdgeInsetsZero];
	mapView.camera = camera;

GMSCameraUpdate: predefined
	GMSCameraUpdate *zoomCamera = [GMSCameraUpdate zoomIn];
	[mapView animateWithCameraUpdate:zoomCamera];
	
	GMSCameraUpdate *updatedCamera = [GMSCameraUpdate setTarget: zoom:];
	[mapView animateWithCameraUpdate: updatedCamera];
	
	GMSCameraUpdate *downwards = [GMSCameraUpdate scrollByX:100 y:200];
	[mapView animateWithCameraUpdate:downwards];

Shapes:
	GMSMutablePath *path = [GMSMutablePath path];
	[path addCoordinate:CLLocationCoordinate2DMake(47.36, -122.0)];
	[path addCoordinate:CLLocationCoordinate2DMake(37.45, -122.0)];
	[path addCoordinate:CLLocationCoordinate2DMake(37.45, 122.2)];
	[path addCoordinate:CLLocationCoordinate2DMake(47.36, 122.2)];
	[path addCoordinate:CLLocationCoordinate2DMake(47.36, -122.0)];
	GMSPolyline *rectangle = [GMSPolyline polylineWithPath:path];
	rectangle.strokeColor = [UIColor redColor];
	rectangle.strokeWidth = 5;
		
	GMSStrokeStyle *redYellow = [GMSStrokeStyle gradientFromColor:[UIColor redColor] toColor:[UIColor yellowColor]];
	GMSStrokeStyle *solidRed = [GMSStrokeStyle solidColor:[UIColor redColor]];
	rectangle.spans = @[[GMSStyleSpan spanWithStyle:redYellow],[GMSStyleSpan spanWithStyle:solidRed]
	                		[GMSStyleSpan spanWithStyle:solidRed segments:2.5]];
	                
	NSArray *styles = @[[GMSStrokeStyle solidColor:[UIColor whiteColor]],
							[GMSStrokeStyle solidColor:[UIColor blackColor]]];
	NSArray *lengths = @[@100000, @50000];
	rectangle.spans = GMSStyleSpans(path, styles, lengths, kGMSLengthRhumb);
    
	rectangle.geodesic = YES;
	rectangle.map = mapView;

Overlay:
	CLLocationCoordinate2D southWest = CLLocationCoordinate2DMake(40.712216,-74.22655);
	CLLocationCoordinate2D northEast = CLLocationCoordinate2DMake(40.773941,-74.12544);
	GMSCoordinateBounds *overlayBounds = [[GMSCoordinateBounds alloc] initWithCoordinate:southWest coordinate:northEast];
	UIImage *icon = [UIImage imageNamed:@"hello.jpg"];
	GMSGroundOverlay *overlay = [GMSGroundOverlay groundOverlayWithBounds:overlayBounds icon:icon];
	overlay.bearing = 0;
	overlay.tappable = YES;
	overlay.map = self.gmsMapView;
	
Tile Layers:
	Map Data.
```

### Markers

```objectivec
// Subclass GMSMarker
@interface HuaGMSMarker : GMSMarker
@property (nonatomic, copy) NSString *objectID;
@end

@implementation HuaGMSMarker
- (BOOL)isEqual:(id)object {
	HuaGMSMarker *otherMarker = (HuaGMSMarker *)object;
	if (self.objectID == otherMarker.objectID) {
		return YES;
	}
	return NO;
}

- (NSUInteger)hash {
	return [self.objectID hash];
}
@end


// User created marker
- (void)mapView:didLongPressAtCoordinate:

// Geocode convert address and coordinate
GMSGeocoder *geocoder = [GMSGeocoder geocoder];
[geocoder reverseGeocodeCoordinate:complentionHandler:^()];
GMSReverseGeocodeCallback handler = ^(GMSReverseGeocodeResponse *response, NSError *error) {
	GMSAddress *address = response.firstResult;
};

// Directions
- (BOOL)mapView:didTapMarker:
```

### StreetView GMSPanorama

```objectivec
// Street View : GMSPanorama
<GMSPanoramaViewDelegate>
GMSPanoramaView *gmsStreetView = [[GMSPanoramaView alloc] initWithFrame:CGRectZero];
gmsStreetView.orientationGestures = YES;
gmsStreetView.zoomGestures = YES;
gmsStreetView.navigationGestures = YES;
gmsStreetView.camera = [GMSPanoramaCamera cameraWithHeading:90 pitch:-10 zoom:1];
gmsStreetView.delegate = self;
self.view = self.gmsStreetView;
[gmsStreetView moveNearCoordinate:CLLocationCoordinate2DMake(-33.732, 150.312)];
```


## Google Places

```objectivec
	GMSPlacesClient
		|- GMSUserAddedPlace
		|- GMSPlacePhotoMetadataList
		|	|- GMSPlacePhotoMetadata
		|- GMSPlace
	
	GMSPlacePicker
		|- GMSPlacePickerConfig

	GMSAutocompleteViewController <GMSAutocompleteViewControllerDelegate>
		|- UISearchController <UISearchDisplayDelegate>
		|- GMSAutocompleteFilter
		|- GMSAutocompletePrediction
		|- GMSAutocompleteResultsViewController <GMSAutocompleteResultsViewControllerDelegate>
		|- GMSAutocompleteTableDataSource <GMSAutocompleteTableDataSourceDelegate>
		|- GMSAutocompleteFetcher <GMSAutocompleteFetcherDelegate>
	
	
```

### Basic Places

```objectivec
GMSPlacesClient *placesClient = [[GMSPlacesClient alloc] init];
[placesClient currentPlaceWithCallback:^(GMSPlaceLikelihoodList * _Nullable likelihoodList, NSError * _Nullable error) {
	if (error!=nil) {
	    NSLog(@"Pick Place error %@", [error localizedDescription]);
	    return;
	}
	    
	if (likelihoodList!=nil) {
	    GMSPlace *place = [[[likelihoodList likelihoods] firstObject] place];
	    if (place != nil) {
	        // ...
	    }
	}
}];

CLLocationCoordinate2D center = CLLocationCoordinate2DMake(37.788204, -122.411937);
CLLocationCoordinate2D northEast = CLLocationCoordinate2DMake(center.latitude + 0.001, center.longitude + 0.001);
CLLocationCoordinate2D southWest = CLLocationCoordinate2DMake(center.latitude - 0.001, center.longitude - 0.001);
GMSCoordinateBounds *viewport = [[GMSCoordinateBounds alloc] initWithCoordinate:northEast coordinate:southWest];
GMSPlacePickerConfig *config = [[GMSPlacePickerConfig alloc] initWithViewport:viewport];
GMSPlacePicker *placePicker = [[GMSPlacePicker alloc] initWithConfig:config];

[placePicker pickPlaceWithCallback:^(GMSPlace * _Nullable result, NSError * _Nullable error) {
	if (error != nil) {
	    NSLog(@"Pick Place error %@", [error localizedDescription]);
	    return;
	}
	    
	if (result != nil) {
		// ...
	} else {
		// ...
	}
}];
```

### JSON Request & Response

```
https://maps.googleapis.com/maps/api/place/search/json?
location=latitud,longitude
&radius=500
&types=food
&sensor=true
&key=API_KEY
```
```
|- html_attributions
|- next_page_token
|- results []
	|- geometry
	|	|- location
	|		|- lat
	|		|- lng
	|- icon
	|- id
	|- name
	|- opening_hours
	|	|- open_now
	|	|- weekday_text []
	|- photos []
	|	|- height
	|	|- html_attributions []
	|	|- photo_reference
	|	|- width
	|- place_id
	|- rating
	|- reference
	|- scope
	|- types []
	|- vicinity		
```

### AutoComplete ViewController

```objectivec
<GMSAutocompleteViewControllerDelegate>
```

## Google Directions

```
https://maps.googleapis.com/maps/api/directions/json?
&origin=latitud,longitude
&destination=latitude,longitude
&sensor=true
&key=API_KEY
```
```
|- geocoded_waypoints []
|- status
|- routes []
	|- bounds
	|	|- northeast
	|	|- southwest
	|- copyrights
	|-	legs []
	|	|- distance
	|	|	|- text
	|	|	|- value
	|	|- duration
	|	|	|- text
	|	|	|- value
	|	|- end_address
	|	|- end_location
	|	|	|- lat
	|	|	|- lng
	|	|- start_address
	|	|-	start_location
	|	|	|- lat
	|	|	|- lng
	|	|- steps []
	|	|	|- distance
	|	|	|	|- text
	|	|	|	|- value
	|	|	|- duration
	|	|	|	|- text
	|	|	|	|- value
	|	|	|- end_location
	|	|	|	|- lat
	|	|	|	|- lng
	|	|	|- html_instructions
	|	|	|- polyline
	|	|	|	|- points
	|	|	|-	start_location
	|	|	|	|- lat
	|	|	|	|- lng
	|	|	|- travel_mode
	|	|- via_waypoint []
	|- overview_polyline
	|	|- points
	|- summary
	|- warnings []
	|- waypoint_order []
		
```
```objectivec
GMSPath *encodedPath = @"formattedString";
GMSPolyline *polyline = [GMSPolyline polylineWithPath:encodedPath];
```




## Example
```objectivec
// Get current location at the beginning
#import <CoreLocation/CoreLocation.h>

<GMSMapViewDelegate, CLLocationManagerDelegate>

@property (nonatomic, strong) CLLocationManager *locationManager;

self.locationManager = [[CLLocationManager alloc] init];
self.locationManager.delegate = self;
self.locationManager.desiredAccuracy = kCLLocationAccuracyBest;
[self.locationManager startUpdatingLocation];

- (void)mapView:(GMSMapView *)mapView didTapAtCoordinate:(CLLocationCoordinate2D)coordinate {
    NSLog(@"You tapped at %f,%f", coordinate.latitude, coordinate.longitude);
}

- (void)locationManager:(CLLocationManager *)manager didUpdateLocations:(NSArray<CLLocation *> *)locations {
    CLLocation *currentLocation = (CLLocation *)[locations lastObject];
    if (currentLocation != nil) {
        NSLog(@"latitude : %.8f, longitude : %.8f .", currentLocation.coordinate.latitude, currentLocation.coordinate.longitude);
        GMSCameraUpdate *updatedCamera = [GMSCameraUpdate setTarget:currentLocation.coordinate zoom:15];
        [self.gmsMapView animateWithCameraUpdate:updatedCamera];
        [self.locationManager stopUpdatingLocation];
    }
}
```
```objectivec
// GMSMapView
@property (nonatomic, strong) GMSMapView *gmsMapView;

self.gmsMapView = [[GMSMapView alloc] initWithFrame:self.view.bounds];
self.gmsMapView.myLocationEnabled = YES;
self.gmsMapView.settings.compassButton = YES;
self.gmsMapView.settings.myLocationButton = YES;
self.gmsMapView.delegate = self;
[self.view addSubview:self.gmsMapView];

- mapView:didTapAtCoordinate:
- mapView:didLongPressAtCoordinate:
- 
```
```objectivec
// markers
@property (nonatomic, copy) NSSet *markers;

self.markers = [[NSSet alloc] init];

for (GMSMarker *marker in self.markers) {
    if (marker.map == nil) {
        marker.map = self.gmsMapView;
    }
}

- (BOOL)mapView:(GMSMapView *)mapView didTapMarker:(HuaGMSMarker *)marker {
	return YES;
}

- (UIView *)mapView:(GMSMapView *)mapView markerInfoWindow:(GMSMarker *)marker {
    UIView *infoWindow = [[UIView alloc] init];
    infoWindow.frame = CGRectMake(0, 0, 200, 70);
    infoWindow.backgroundColor = [UIColor greenColor];
    
    UILabel *titleLabel = [[UILabel alloc] init];
    titleLabel.frame = CGRectMake(14, 11, 175, 16);
    titleLabel.text = marker.title;
    [infoWindow addSubview:titleLabel];
    
    UILabel *snippetLabel = [[UILabel alloc] init];
    snippetLabel.frame = CGRectMake(14, 42, 175, 16);
    snippetLabel.text = marker.snippet;
    [infoWindow addSubview:snippetLabel];
    
//    UIImageView *backgroundImage = [[UIImageView alloc] initWithImage:[UIImage imageNamed:@"size200"]];
//    [infoWindow addSubview:backgroundImage];
    
    return infoWindow;
}

- (void)mapView:(GMSMapView *)mapView didTapInfoWindowOfMarker:(GMSMarker *)marker {
    NSLog(@"You tapped the info window fro the %@ marker.", marker.title);
}
```
```objectivec
// GMSGeocoder
GMSGeocoder *geocoder = [GMSGeocoder geocoder];
[geocoder reverseGeocodeCoordinate:coordinate completionHandler:^(GMSReverseGeocodeResponse *response, NSError *error) {
	response.firstResult.thoroughfare;
	response.firstResult.locality;
}];
```
```objectivec
// Street View : GMSPanorama
GMSPanoramaService *service = [[GMSPanoramaService alloc] init];
[service requestPanoramaNearCoordinate:self.userMarker.position callback:^(GMSPanorama *panorama, NSError *error) {
    if (panorama != nil) {
        GMSPanoramaCamera *camera = [GMSPanoramaCamera cameraWithHeading:180 pitch:0 zoom:1 FOV:90];
        GMSPanoramaView *panoView = [[GMSPanoramaView alloc] init];
        panoView.camera = camera;
        panoView.panorama = panorama;
        UIButton *closeStreetViewButton = [UIButton buttonWithType:UIButtonTypeSystem];
        [closeStreetViewButton addTarget:self action:@selector(closeView:) forControlEvents:UIControlEventTouchUpInside];
        [self.view addSubview:panoView];
        [self.view addSubview:closeStreetViewButton];
    }
}];
```

## Google Examples
```objectivec
// My Location
GMSCameraPosition *camera = [GMSCameraPosition cameraWithLatitude:-33.868 longitude:151.2086 zoom:12];
mapView = [GMSMapView mapWithFrame:CGRectZero camera:camera];
mapView.settings.compassButton = YES;
mapView.settings.myLocationButton = YES;
[mapView addObserver:self forKeyPath:@"myLocation" options:NSKeyValueObservingOptionNew context:nil];
self.view = mapView;
dispatch_async(dispatch_get_main_queue(), ^{
	mapView.myLocationEnabled = YES;
});

- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary *)change context:(void *)context {
	if(!firstLocationUpdate) {
		firstLocationUpdate = YES;
		CLLocation *location = [change objectForKey:NSKeyValueChangeNewKey];
		mapView.camera = [GMSCameraPosition cameraWithTarget:location.coordinate zoom:14];
	}
}
```
```objectivec
// Snapshot
// Take a snapshot of the map.
UIGraphicsBeginImageContextWithOptions(mapView.bounds.size, YES, 0);
[mapView drawViewHierarchyInRect:mapView.bounds afterScreenUpdates:YES];
UIImage *mapSnapShot = UIGraphicsGetImageFromCurrentImageContext();
UIGraphicsEndImageContext();

// Put snapshot image into an UIImageView and overlay on top of map.
UIImageView *imageView = [[UIImageView alloc] initWithImage:mapSnapShot];
imageView.layer.borderColor = [UIColor redColor].CGColor;
imageView.layer.borderWidth = 10.0f;
[mapView addSubview:imageView];

// Remove imageView after 1 second.
dispatch_after(dispatch_time(DISPATCH_TIME_NOW, 1 * NSEC_PER_SEC), dispatch_get_main_queue(), ^{
[UIView animateWithDuration:1
    animations:^{
      imageView.alpha = 0.0f;
    }
    completion:^(BOOL finished) {
      [imageView removeFromSuperview];
    }];
});
```
```objectivec
// Toggle Overlay View
CGRect overlayFrame = CGReckMake(0, -100, 0, 100);
UIView overlay = [[UIView alloc] initWithFrame:overlayFrame];
[self.view addSubview:overlay];

[UIView animateWithDuration:1.0 animations:^{
	CGSize size = self.view.bounds.size;
	if (padding.bottom == 0.0f) {
		overlay.frame = CGReckMake(0, size.height - 100, size.width, 100);
		mapView.padding = UIEdgeInsetsMake(0, 0, 100, 0);
	} else {
		overlay.frame = CGReckMake(0, mapView.bounds.size.height, size.width, 0);
		mapView.padding = UIEdgeInsetsZero;
	}
}];
```
```objectivec
// Fit bounds
GMSCoordinateBounds *bounds = [[GMSCoordinateBounds alloc] initWithCoordinate:p1 coordinate:p2];
bounds = [bounds includingCoordinate:p];
GMSCameraUpdate *update = [GMSCameraUpdate fitBounds:bounds withPadding:50.0f];
[mapView moveCamera:update];
```
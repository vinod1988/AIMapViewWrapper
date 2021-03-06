#AIMapViewWrapper

- Use Google maps zoom levels with your MKMapView
- Add path overlay views to your MKMapView with an animated vehicle on it (useful for flight route tracking)
- Get use of some other map view related functions

#Requirements

Classes in this project supports both ARC and non-ARC projects

#Installation

Add the AIMapViewWrapper folder to your project

#How to use it?

##AIMapViewWrapper - Zoom level

    #import "AIMapViewWrapper.h"
    
    //create a AIMapViewWrapper instance with your MKMapView object
    self.mapWrapper = [[AIMapViewWrapper alloc] initWithMapView:self.mapView];
    
    //set a google maps zoom level
    //valid zoom level values are between a minimum zoom level and maximum 19
    //minimum zoom level changes in accordance with your map view's bounds
    self.mapWrapper.zoomLevel = 5;
    
    //in your MKMapView's delegate method listen for changes in zoomLevel
    - (void)mapView:(MKMapView *)mapView regionDidChangeAnimated:(BOOL)animated {
    
        self.zoomLevelSlider.value = self.mapWrapper.zoomLevel;
        
    }
    
    //become a delegate of your map view wrapper
    self.mapWrapper.delegate = self;
    
    //respond to changes of minimum zoom level that can be set on your MKMapView
    - (void)mapViewWrapperMinimumZoomLevelChanged:(AIMapViewWrapper *)wrapper {
    
        NSInteger zoomLevel = wrapper.zoomLevel;
    }
    
##AIOverlayRouteView

AIOverlayRouteView is a MKOverlayPathView subclass which you can use to add an animated vehicle over a path on your map view

    #import "AIRoute.h"
    #import "AIOverlayRouteView.h"
    
    //Create a AIRoute (a MKOverlay) and add it to your map view
    AIRoute *route = [AIRoute routeWithCoordinates:/*array of CLLocation objects*/];
    [self.mapView addOverlay:route];
    
    //in your map view's delegate method create an AIOverlayRouteView with the added route overlay
    - (MKOverlayView *)mapView:(MKMapView *)mapView viewForOverlay:(id <MKOverlay>)overlay {
        if ([overlay isKindOfClass:[AIRoute class]]) {
            AIOverlayRouteView *pathView = [[AIOverlayRouteView alloc] initWithRoute:overlay vehicleImage:[UIImage imageNamed:@"plane.png"]];
            pathView.delegate = self;
            pathView.strokeColor = [[UIColor whiteColor] colorWithAlphaComponent:0.7f];
            pathView.lineCap = kCGLineCapRound;
            pathView.lineWidth = 6.0f;
            
            return pathView;
        }
        return nil;
    }
    
    //in your map view's delegate method call AIOverlayRouteView's layout method for proper subview layout
    //using AIMapViewWrapper's zoomScale value
    - (void)mapView:(MKMapView *)mapView regionDidChangeAnimated:(BOOL)animated {
    
        AIOverlayRouteView *routeView = (AIOverlayRouteView *)[mapView viewForOverlay:self.route];
        
        [routeView layoutWithZoomScale:self.mapWrapper.zoomScale];
    }
    
    //in your AIRouteOverlay delegate method start the vehicle animation
    //you can start this animation any time you want after this delegate method is called
    - (void)overlayRouteViewIsReadyForAnimation:(AIOverlayRouteView *)routeView {
        
        //provide animation time intervals
        [routeView startAnimationWithTotalTravelTime:40000 
                                 remainingTravelTime:20000];
    }
    
    //you can track the vehicle as its position changes
    //with this map view's zoom level focuses on the moving vehicle
    self.mapWrapper.trackedRoute = self.route;
    
    
#Screenshots

![Screenshot 1](http://f.cl.ly/items/451S010h260d0b1w032g/1.png)
![Screenshot 2](http://f.cl.ly/items/3q433Q0L2D1T0G3N0y25/2.png)

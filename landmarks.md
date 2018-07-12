# Landmarks

Landmarks are geographical points of interest (POIs) defined by a perimeter. Landmarks can be used as conditions to rules that evaluate a device's proximity to an area. A rule may check if a device has entered, exited, is inside, or outside the landmark. The Landmark API provides several options for defining landmarks.

*Please double-check your coordinates when creating landmarks via the API. It may be helpful to check the dashboard and verify that they are created in the desired location.*

Example:
```json
{
    "address": "123 Lane Rd",
    "model": {
        "geometry": {
            "center": {
                "lat": 34.069076,
                "lon": -118.444846
            },
            "radius": 750
        },
        "type": "circle"
    },
    "name": "UCLA",
    "type": "geometry"
}
```
Fields:
* type - "geometry"
* name - (required) Name of the landmark
* address - (optional) 
* model - (required) Specifies the geometrical model this landmarks is based on.
  * geometry - (required) - Specifies the coordinates for this particular shape. [See Geometry Models](#geometry-models) for more information on required fields for each type
  * type - (required) Shape of this landmark. Must be one of the following: circle, rectangle, polygon   
 
 
## Geometry Models

The model describes the geometric shape and geographic location surrounding the perimeter of the landmark.

### circle

Description: A landmark defined by a circle around a central latitude and longitude. Radius is in **meters**.

Fields:
* type - "circle"
* geometry - (required)
	* center - the coordinates (lat, long) specifying the center of the circle
* radius - the radius of the circle in meters

> The following example creates a circle with a radius of 750m around the point 34.069076, -118.444846.

Example:
```json
"model": {
	"geometry": {
		"center": {
			"lat": 34.069076,
  			"lon": -118.444846
  		},
		"radius": 750
  	},
	"type": "circle"
}
```

### rectangle

Description: A landmark defined by a rectangle specified by its northwestern (NW) and southeastern (sw) points.

Fields:
* type - "circle"
* geometry - (required)
	* nw - (required) the coordinates (lat, long) specifying the northwestern most point of this rectangle
	* sw - (required) the coordinates (lat, long) specifying the southwestern most point of this rectangle
	
> The following example creates a rectangle around Marina Del Rey using the northwestern and southeasten coordinates of the area.
	
```json
"model": {
	"geometry": {
    		"nw": {
    			"lat": 33.98709612420996,
			"lon": -118.46703218199536
    		},
    		"se": {
			"lat": 33.96460416426154,
			"lon": -118.44797776915355
    		}
	},
  	"type": "circle"
}
```

### polygon

Description: A landmark defined by a polygon specified by a series of points.

Fields:
* type - "polygon"
* geometry - (required)
	* points - an set of coordinates specifying the perimeter of the polygon

> The following example creates a polygon around the specified area of the Santa Monica Pier.

```json
"model": {
	"type": "polygon",
	"geometry": {
		"points": [{
			"lat": 34.00849749651098,
			"lon": -118.49859443652542
		}, {
			"lat": 34.00983154378065,
			"lon": -118.49699583995255
		}, {
			"lat": 34.010498559556034,
			"lon": -118.49594441401871
		}, {
			"lat": 34.00990269237967,
			"lon": -118.49527922618302
		}, {
			"lat": 34.00770595189094,
			"lon": -118.49803653705033
		}, {
			"lat": 34.00781267743858,
			"lon": -118.49832621562393
		}, {
			"lat": 34.00804391566499,
			"lon": -118.49820819842728
		}, {
			"lat": 34.00831962195817,
			"lon": -118.49847641932877
		}, {
			"lat": 34.00725236681703,
			"lon": -118.49965659129532
		}, {
			"lat": 34.007536969499505,
			"lon": -118.50026813495072
		}, {
			"lat": 34.0078304650168,
			"lon": -118.50001064288529
		}, {
			"lat": 34.007874933946106,
			"lon": -118.49976387965592
		}, {
			"lat": 34.008132853276734,
			"lon": -118.49921670901688
		}, {
			"lat": 34.008470815351814,
			"lon": -118.49869099604996
		}, {
			"lat": 34.00863090218105,
			"lon": -118.49845496165665
		}]
	}
}
```

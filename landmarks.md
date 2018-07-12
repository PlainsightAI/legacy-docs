# Landmarks

Landmarks are geographical points of interest (POIs). Landmarks are used as conditions for a rule that evaluate a device's proximity to the area. The Landmark API provides several options for defining landmarks.

Example:
```json
{
    "address": "",
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
  * geometry - (required) - Specifies the coordinates for this particular shape. See geometry section below for more information on required fields for each type
  * type - (required) Shape of this landmark. Must be one of the following: circle, rectangle, polygon
  
 
## Geometry

### circle

A landmark defined by a circle around a central latitude and longitude. Radius is in **meters**.

> The following example creates a circle with a radius of 750m around the point 34.069076, -118.444846.

Example:
```json
"geometry": {
	"center": {
            "lat": 34.069076,
            "lon": -118.444846
        },
        "radius": 750
}
```

Fields:
* center - the coordinates (lat, long) specifying the center of the circle
* radius - the radius of the circle in meters

### rectangle

A landmark defined by a rectangle specified by its northwestern (NW) and southeastern (sw) points.

> The following example creates a rectangle with a northwest point of 33.98709612420996,-118.46703218199536 and southeastern coordinates of 33.96460416426154,-118.44797776915355

```json
"geometry": {
    "nw": {
    	"lat": 33.98709612420996,
	"lon": -118.46703218199536
    },
    "se": {
	"lat": 33.96460416426154,
	"lon": -118.44797776915355
    }
}
```
Fields:
* nw - the coordinates (lat, long) specifying the northwestern most point of this rectangle
* sw - the coordinates (lat, long) specifying the southwestern most point of this rectangle


### polygon

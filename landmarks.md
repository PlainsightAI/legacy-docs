# Landmarks

Landmarks are geographical points of interest (POIs). Proximity to Landmarks is used in Rules to 
trigger an action such as content delivery to an agent or webhook to another service. The Landmark API provides several options 
for defining landmarks.

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
* model - (required)
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

### rectange

### polygon

# Ingress API

## Introduction

The Ingress API allows devices to register with and send data to the Sixgill Sense platform. Currently, the API is is compatible with mobile (iOS, Android) and generic IoT devices, and it supports both JSON and [Protobuf](https://developers.google.com/protocol-buffers/) formatted data.

The API is separated into two main sections: mobile and IoT. The former is intended to be used by the Sixgill Reach SDKs for iOS and Android; the latter is a single endpoint that ingests generic JSON data. Both sections register devices the same way.

## Mobile

### Authentication

To register with the Ingress API, devices must send their info and a valid API Key, which can be generated in the "channels" section of the Dashboard, to the "registration" endpoint. In exchange they'll receive a JSON Web Token with which they can interact with the remainder of the app. Below is a sample registration request:

```shell
curl -X POST "https://sense-ingress-api.sixgill.com/v1/registration"  -d '{
    "apiKey":"EXAMPLE_API_KEY",
    "properties":{
      "timestamp":1,
      "manufacturer":"Apple",
      "model":"iPhone X",
      "os":"iOS",
      "osVersion":"11.0.0",
      "softwareVersion":"ReachSDK-v1.2.3",
      "type":"iOS",
      "sensors":["location","wifi","beacon","power","activity"]
  }
}'
```

A successful registration returns a JSON Web Token, the id for the newly registered device and the id of the organization with which it was registered. E.g.:

```json
{
    "token":"EXAMPLE_JWT_TOKEN",
    "deviceId":"01BV2XZY0N85EW8B1NHTHSCDCM",
    "organizationId":"01BV2XZY0N85EW8B1NHTHSCDCM"
}
```

Devices can then authenticate HTTP requests by placing this token in their Authorization headers, like so:

```shell
curl "https://sense-ingress-api.sixgill.com/v1/mobile/configuration"  -H "Authorization: Bearer EXAMPLE_JWT_TOKEN"
```

### Protobuf Support

JSON is the default serialization format for the Ingresss API, but the mobile endpoints support Protobuf as well. The API serves Protobuf content if the request's Accept header is set to "application/protobuf." To send data as Protobuf, the request's Content-Type header must also be set to "application/protobuf".

> Tell the API that you can accept a protobuf reponse:
```shell
curl "https://sense-ingress-api.sixgill.com/v1/mobile/configuration"  -H "Authorization: Bearer EXAMPLE_JWT_TOKEN" -H "Accept: application/protobuf"
```

> Tell the API that you are sending protobuf in the body:
```shell
curl -X POST "https://sense-ingress-api.sixgill.com/v1/mobile/events" -H "Authorization: Bearer EXAMPLE_JWT_TOKEN" -H "Content-Type: application/protobuf"
```

### POST /v1/mobile/events

Mobile devices can create "events" that fire on a cadence specified in their configuration. Events send information on a device's location, power and activity state, as well as timestamped readings of nearby beacons and wifis. Since events fire "updates" in FIFO order, downstream data processing can assume that each new update is the most recent. Updates do not include event configuration, device properties or user attributes unless one of them has changed since the last update. NOTE: Be sure that **timestamp** is in milliseconds

> POST request of event sensor data within a collection period:

```shell
curl -X POST "https://sense-ingress-api.sixgill.com/v1/mobile/events" -H "Authorization: Bearer EXAMPLE_JWT_TOKEN" -d '{
  "timestamp":123456789,
  "locations":[{
    "timestamp":123456789,
    "latitude":85.0,
    "longitude":112.3,
    "velocity":12.2,
    "course":77.9,
    "accuracy":10.4
  }],
  "wifis":[{
    "timestamp":123456789,
    "mac":"FF:FF:FF:FF:FF:FF",
    "ssid":"Sixgill Wifi",
    "rssi":-20
  }],
  "ibeacons":[{
    "timestamp":123456789,
    "uuid":"123e4567-e89b-12d3-a456-426655440000",
    "rssi":-20,
    "major":123,
    "minor":124,
    "proximity":"near",
    "accuracy":10.0
  }],
  "powers":[{
    "timestamp":123456789,
    "charging":false,
    "batteryLevel":91
  }],
  "activities":[{
    "timestamp":123456789,
    "type":"walking",
    "confidenceLevel":"low",
    "confidencePercentage":62.0
  }],
  "configuration":{
    "enabled":true,
    "cadence":60000,
    "useSensors":["location","wifi","beacon","power","activity"],
    "eventTTL":6000000,
    "maxStorage":6000000
  },
  "properties":{
    "timestamp":123456789,
    "manufacturer":"Apple",
    "model":"iPhone X",
    "os":"iOS",
    "osVersion":"11.0.0",
    "softwareVersion":"ReachSDK-v1.2.3",
    "type":"iOS",
    "sensors":["location","wifi","beacon","power","activity"]
  },  
  "push":{
    "timestamp":123456789,
    "token":"5311839E985FA01B56E7AD74334C0137F7D6AF71A22745D0FB50DED665E0E882",
    "type":"ios-production"
  }
  "attributes":{
    "email":"lspears@sixgill.com",
    "gender":"male",
    "age":26
  }
}'
```

> A successful update will return a 204 NO CONTENT Status Code

### GET /v1/mobile/configuration

The Ingress API assigns every mobile device a configuration. This configuration specifies whether or not the device's SDK should be enabled; how often the device should send data, or its collection cadence; what type of sensors it should use; and how long its local data should persist, or its event TTL.

> GET request for the configuration
```shell
curl "https://sense-ingress-api.sixgill.com/v1/mobile/configuration" -H "Authorization: Bearer EXAMPLE_JWT_TOKEN"
```

> Resulting JSON
```json
{
    "enabled":true,
    "cadence":60000,
    "sensors":["location","wifi","iBeacon","power","activity"],
    "eventTTL":6000000,
    "maxStorage":6000000
}
```

### GET /v1/mobile/ibeacons?count=20

The iBeacons endpoint returns beacons most relevant to the state of the device specified in the JWT. One can limit the amount of iBeacons received with a 'count' url parameter. N.B.: in order to listen for beacon UUIDs, iOS requires that the beacons be whitelisted.   

> GET request for whitelist of beacons
```shell
curl "https://sense-ingress-api.sixgill.com/v1/mobile/ibeacons" -H "Authorization: Bearer EXAMPLE_JWT_TOKEN"
```

> Resulting JSON
```json
{
  "ibeacons":[{
    "uuid":"123e4567-e89b-12d3-a456-426655440000",
    "rssi":-20,
    "major":123,
    "minor":124
  }]
}
```

## IoT

### POST /v1/iot/events

IoT devices send data "updates" to the IoT events endpoint. This endpoint accepts said data in a schema agnostic format and sends it directly to the ingestion pipeline.  

```shell
curl -X POST "https://sense-ingress-api.sixgill.com/v1/iot/events" -H "Authorization: Bearer EXAMPLE_JWT_TOKEN" -d '
{
  "data":{
    "tempF":72,
    "humidity":23
  }
}
'
```

> A successful update will return a 204 NO CONTENT Status Code

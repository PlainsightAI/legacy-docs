# Ingress API

## Introduction

The Ingress API allows devices to register with Sixgill Sense, send device event data, and recieve configuration and other required metadata.  It supports event data from mobile (currently iOS and Android) and generic data from IoT devices.  The Ingress API exposes a HTTP REST interface that accepts requests and sends responses in JSON with [Protobuf](https://developers.google.com/protocol-buffers/) being additional supported for mobile.  

The API is deviced into two sections: mobile and IoT.  The mobile portion of the API is intended to be used by the Sixgill Reach SDKs for iOS and Android.  The Iot section consists of a single endpoint /iot/events which ingests generic JSON data.  Both sections share the same device registration flow.    

## Mobile

### Authentication

Devices supply an API Key and a token is returned for interacting with the rest of the API.  An API Key belongs to a channel and can be generated using the dashboard.  

> API Key and device properties supplied to the registration endpoint

```shell
curl -X POST "https://ingress.sixgill.com/v1/registration"  -d '{
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

> A successful registration will generate a JWT (JSON Web Token) and return the organization id and device id:

```json
{
    "token":"EXAMPLE_JWT_TOKEN",
    "deviceId":"01BV2XZY0N85EW8B1NHTHSCDCM",
    "organizationId":"01BV2XZY0N85EW8B1NHTHSCDCM"
}
```

> Authenticated endpoints check for the token in the Authorization header:

```shell
curl "https://ingress.sixgill.com/v1/mobile/configuration"  -H "Authorization: Bearer EXAMPLE_JWT_TOKEN"
```

Ref
- TODO link to properties

### Protobuf Support

JSON is the default serialization format for the Ingresss API, but mobile endpoints support Protobuf as well.  The API serves Protobuf content if the Accept header is set to "application/protobuf".  "Content-Type" is required for the request to be correctly parsed.  

> Tell the API that you can accept a protobuf reponse:
```shell
curl "https://ingress.sixgill.com/v1/mobile/configuration"  -H "Authorization: Bearer EXAMPLE_JWT_TOKEN" -H "Accept: application/protobuf"
```

> Tell the API that you are sending protobuf in the body:
```shell
curl -X POST "https://ingress.sixgill.com/v1/mobile/events" -H "Authorization: Bearer EXAMPLE_JWT_TOKEN" -H "Content-Type: application/protobuf"
```

### POST /v1/mobile/events

Mobiles devices create an event on a cadence set by the configuration.  The device reports location, power, and activity state readings as well as nearby beacons and wifis.  Each reading is timestamped with the time of collection.  Mobile updates are guaranteed to be in FIFO order so downstream data processing can assume each update is the most recent.  Configuration, properties, and attributes are only sent if the values have changed.  

> POST request of event sensor data within a collection period:

```shell
curl -X POST "https://ingress.sixgill.com/v1/mobile/events" -H "Authorization: Bearer EXAMPLE_JWT_TOKEN" -d '{
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

Ref
- TODO link to events

### GET /v1/mobile/configuration

Mobile devices are assigned a configuration from the server.  The configuration controls the sensor collection cadence, which sensors to use, TTL of local data, and whether the SDK should be enabled at all.

> GET request for the configuration
```shell
curl "https://ingress.sixgill.com/v1/mobile/configuration" -H "Authorization: Bearer EXAMPLE_JWT_TOKEN"
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

Ref
- TODO link to config

### GET /v1/mobile/ibeacons?count=20

iOS requires beacon UUIDs to be whitelisted in order to listen for them.  The iBeacons endpoint returns beacons most relevent to the device state.  A count can be supplied to limit the response.  

> GET request for whitelist of beacons
```shell
curl "https://ingress.sixgill.com/v1/mobile/ibeacons" -H "Authorization: Bearer EXAMPLE_JWT_TOKEN"
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

Ref
- TODO link to beacons

## IoT

### POST /v1/iot/events

IoT devices post data to the IoT events endpoint.  The data is schemaless and send directly to the pipeline.  

```shell
curl -X POST "https://ingress.sixgill.com/v1/iot/events" -H "Authorization: Bearer EXAMPLE_JWT_TOKEN" -d '
{
  "data":{
    "tempF":72,
    "humidity":23
  }
}
'
```

> A successful update will return a 204 NO CONTENT Status Code
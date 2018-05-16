# Sensors

The CogniCity Sensors endpoints supports centralizing data from automated sensor networks. Sensor data can be inserted into the database for storage, and subsequently served as GeoJSON or TopoJSON to support map visualisations.

Sensor locations are point geometries defined using latitude and longitude coordinates in the WGS 1984 coordinate system.

<aside class="notice">
Note that these endpoints reside on a separate sensors subdomain.
</aside>


## Get All Sensors

```shell
curl "/?bbox=-81,27,-79,25&geoformat=geojson"

```

```javascript
import axios from 'axios'; // package to make http requests

axios.get('/', {
  params: {
    bbox: '-81,27,-79,25',
    geoformat: 'geojson'
    }
  })
  .then(response => console.log(response))
  .catch(err => console.log(err))

```

> The response body contains the following JSON object:

```json
{
    "statusCode": 200,
    "result": {
        "type": "FeatureCollection",
        "features": [
          {
            "type": "Feature",
            "geometry": {
              "type": "Point",
              "coordinates": [
                -80.3853056,
                26.0341111
              ]
            },
            "properties": {
              "id": "2",
              "created": "2017-10-24T22:35:58.875Z",
              "properties": {
                "uid": "260653080184901",
                "type": "GW",
                "class": "62610",
                "units": "ft"
              }
            }
          },
          {
            "type": "Feature",
            "geometry": {
              "type": "Point",
              "coordinates": [
                -80.1153333,
                26.31780556
              ]
            },
            "properties": {
              "id": "3",
              "created": "2017-10-24T22:35:58.883Z",
              "properties": {
                "uid": "261903080065601",
                "type": "GW",
                "class": "62610",
                "units": "ft"
              }
            }
          }
        ]
    }
}
```

This endpoint returns a collection of sensors and their properties within the specified bounding box. If no bounding box is provided all sensors in the database will be returned.

### HTTP Request

`GET /`

### Query Parameters

Parameter | Required | Description | Default | Example |
--------- | ------- | ------------ | ------- | ------- |
bbox | false | A list of longitude and latitude pairs representing the top left and bottom right corners of a bounding box for sensor locations. | none | longitude, latitude, longitude, latitude
geoformat | false | Specifies either 'geojson' or 'topojson' | geojson | geojson |

### Response
The returned data uses the GeoJSON FeatureCollection or TopoJSON GeometryCollection types. The properties of each sensor feature as described in the following table.

Attribute | Type | Description |
--------- | --------- | ----------- |
id | integer | Unique sensor identifier |
created | string | timestamp sensor created in database (ISO 8601 format) |
properties | json | metadata attributes for each sensor |

## Get Sensor Data

```shell
curl "/3"
```

```javascript
import axios from 'axios'; // package to make http requests

axios.get('/3')
  .then(response => console.log(response))
  .catch(err => console.log(err))
```

> The above command returns JSON structured like this:

```json
{
  "statusCode": 200,
  "result": [
    {
      "id": "24439",
      "sensor_id": "3",
      "created": "2018-03-16T17:37:47.116Z",
      "properties": {
        "observations": [
          {
            "value": "3.84",
            "dateTime": "2018-03-15T13:45:00.000-04:00"
          },
          {
            "value": "3.84",
            "dateTime": "2018-03-15T14:00:00.000-04:00"
          }
        ]
      }
    }
  ]
}
```

This endpoint retrieves a list of data for a specific sensor. Data are organised into records, indexed by the `id` attribute. The `created` attribute details when the record was last updated. The `properties` attribute contains the data for the record. The `properties` object may be in varying formats. The example shows a `properties` object for a single data record which contains an `observations` array of measurements.

### HTTP Request

`GET /<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the sensor to retrieve

### Response
Attribute | Type | Description |
--------- | --------- | ----------- |
id | integer | Unique data record identifier |
sensor_id | integer | The ID of the sensor |
created | string | Data record update time (ISO 8601 format) |
properties | object | Record data |

## Create Sensor

```shell
curl -X POST / -H 'Content-Type: application/json' -H 'x-api-key: key' -d '{
	"properties":{
		"name": "test sensor"
	},
	"location":{
		"lat": 25.0,
		"lng": -80.0
	}
}'

```

```javascript
import axios from 'axios'; // package to make http requests

axios.post('/', {
    properties: {
      name: "test sensor"
    },
    location: {
      lat: 25.0,
      lng: -80.0
    }
  }, {headers: {'x-api-key': 'key'}})
  .then(response => console.log(response))
  .catch(err => console.log(err))
```

> The above command returns JSON structured like this:

```json
{
    "statusCode": 200,
    "result": {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "geometry": {
                    "type": "Point",
                    "coordinates": [
                        -80.0,
                        25.0
                    ]
                },
                "properties": {
                    "id": "4",
                    "created": "2018-03-16T18:25:07.613Z",
                    "properties": {
                        "name": "test sensor"
                    }
                }
            }
        ]
    }
}
```

This endpoint creates a new sensor. On success a GeoJSON feature representing the new sensor is returned.

### HTTP Request

`POST / `

<aside class="success">
This request requires an API key for authorization using the `x-api-key` header.
</aside>


### New Sensor Attributes
Attribute | Type | Description | Required
--------- | ---- | ----------- | --------
properties | object | Sensor metadata properties | Yes
location | object | An object with `lat` and `lng` numerical values representing the sensor's latitude and longitude | Yes


## Add Sensor Data

```shell
curl -X POST /4 -H 'Content-Type: application/json' -H 'x-api-key: key' -d '{
	"properties":{
		"METAR": "CYYQ 182200Z 07002KT 15SM OVC017 06/03 A2966 RMK SC10 3 POLAR BEARS ALNG RNWY=="
  }
}'

```

```javascript
import axios from 'axios'; // package to make http requests

axios.post('/4', {
    properties: {
      METAR: "CYYQ 182200Z 07002KT 15SM OVC017 06/03 A2966 RMK SC10 3 POLAR BEARS ALNG RNWY=="
    },
  }, {headers: {'x-api-key': 'key'}})
  .then(response => console.log(response))
  .catch(err => console.log(err))

```

> The above command returns JSON structured like this:

```json
{
    "statusCode": 200,
    "result": {
            "id": "4",
            "dataId": "240288",
            "created": "2018-03-16T19:30:33.042Z"
    }
}
```

This endpoint adds a new data record to the specified sensor. On success the sensor ID and an updated timestamp are returned.

<aside class="success">
This request requires an API key for authorization using the `x-api-key` header.
</aside>

### HTTP Request

`POST /<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the sensor

### New Sensor Attributes

Attribute | Type | Description | Required
--------- | ---- | ----------- | --------
properties | object | New data record to be added | Yes

## Delete Sensor Data

```sh
curl -X DELETE /4/2345 -H 'Content-Type: application/json' -H 'x-api-key: key'

```

```javascript
import axios from 'axios'; // http requests
// delete record 2345 from sensor 4
axios.delete('/sensors/4/2345', {headers: {'x-api-key': 'key'}, {'content-type': 'application/json'}})
  .then(response => console.log(response))
  .catch(err => console.log(err))
```

```json
{
    "statusCode": 200,
    "body": {}
}
```
This endpoint deletes a data record for the specified sensor. On success the statusCode and an empty body object are returned.

<aside class="success">
This request requires an API key for authorization using the `x-api-key` header.
</aside>

### HTTP Request

`DELETE /<ID>/<DATAID>`
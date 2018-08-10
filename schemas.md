# Schemas

Device data needs to be mapped to fields so you can trigger rules and view analytics. 
This is done using Schemas. Mobile devices (iOS, Android) have predefined schemas that you can use with our SDK a
nd you don’t need to configure them.

For IoT devices you need to first create a custom schema. You can send any kind of data with IoT devices.
It is recommended that you generate IoT schema based on your event payload. 
Once your schema is defined you can use it in a Channel and see data being ingested. 
You can update a schema if necessary at a later time. You will need to include all the 

## Creating a Schema

To create a schema through the dashboard
1. Go to **Channels**
2. In the left sidebar under Schemas, click **+ Add Schema**

![](images/schema_adding_schema.png)

3. **Name & Details** - Enter a name for the schema in "Name" field
4. **Define Fields** - Use this section to specify the format of your schema


Here is a simple example:
To define a schema for a JSON payload such as
```json
 "data":{
      "tempF":52,
      "humidity":23
    }
```
add each attribute individually under the **Define Fields** section.

- Click **Add new attribute**  
- In the **Attribute Location** drop-down, ensure that "root" is selected
- In the **Attribute Identifier** field, enter "data"
- In the **Data Type** drop-down, select "Object"
- Click **Create Attribute** 
Next, add the second attribute named "tempF" the same way:  
- Click **Add new attribute**
- In the **Attribute Location** drop-down, select "data"
- In the **Attribute Identifier** field, enter "tempF"
- In the **Data Type** drop-down, select "Integer"
- Click **Create Attribute**

Add the final attribute named "humidity" in a similar manner. 

5. When finished, click **Create Schema**  Your schema should look like the following:

![](images/schema_json_schema_example.png)

To create a schema using the API:

##### POST /v2/schemas

Fields:
* name - the name of this schema
* format - "json" for a JSON formatted data
* fields - the object representation of the structure of your data. Supports multiple nested fields.
	* id - the name of your attribute
	* type - the type of attribute: 'object', 'string', 'float', 'integer', 'timestamp', 'datetime'
```json
{
	"name": "my schema",
	"format": "json",
	"fields": [{
		"id": "data",
		"type": "object",
		"fields": [{
			"id": "humidity",
			"type": "integer"
		}, {
			"id": "tempF",
			"type": "integer"
		}]
	}]
}
```
`Code: 201`
```json
{
	"data": {
		"id": "01CMDMCSF92P5ZSHKE119R9BTE",
		"name": "test 3",
		"format": "json",
		"organizationId": "01C54H1MTA8K8ZZ0B7WNB3P0N6",
		"fields": [{
			"id": "data",
			"type": "object",
			"fields": [{
					"id": "humidity",
					"type": "integer"
				},
				{
					"id": "tempF",
					"type": "integer"
				}
			]
		}],
		"createdAt": "2018-08-08T20:42:03Z",
		"updatedAt": "2018-08-08T20:42:03Z"
	}
}
```

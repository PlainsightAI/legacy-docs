# Schemas

Device data needs to be mapped to fields so you can trigger rules and view analytics. 
This is done using Schemas. Mobile devices (iOS, Android) have predefined schemas that you can use with our SDK a
nd you don’t need to configure them.

For IoT devices you need to first create a custom schema. You can send any kind of data with IoT devices.
It is recommended that you generate IoT schema based on your event payload. 
Once your schema is defined you can use it in a Channel and see data being ingested. 
You can update a schema if necessary at a later time.

## Creating a Schema

To create a schema through the dashboard
1. Go to **Channels**
2. In the left sidebar under Schemas, click **+ Add Schema**

**SCREENSHOT**

3. **Name & Details** - Enter a name for the schema in "Name" field
4. Select **JSON** or **Query String** radio button depending on the schema you wish to create
5. **Define Fields** - Use this section to specify the format of your schema


Here is a simple example:
To define a schema for a payload such as
```json
 "data":{
      "tempF":52,
      "humidity":23
    }
```
add each attribute individually.

1. Click **Add new attribute**
2. In the **Attribute Location** drop-down, ensure that "root" is selected
3. In the **Attribute Identifier** field, enter "data"
4. In the **Data Type** drop-down, select "Object"
5. Click **Create Attribute** 
Next, add the second attribute named "tempF" the same way:  
6. Click **Add new attribute**
7. In the **Attribute Location** drop-down, select "data"
8. In the **Attribute Identifier** field, enter "tempF"
9. In the **Data Type** drop-down, select "Integer"
10. Click **Create Attribute**

Add the final attribute named "humidity" in a similar manner. Your schema should look like the following

**SCREENSHOT*


To create a schema using the API, 

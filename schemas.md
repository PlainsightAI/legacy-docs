# Schemas

Device data needs to be mapped to fields so you can trigger rules and view analytics. 
This is done using Schemas. Mobile devices (iOS, Android) have predefined schemas that you can use with our SDK a
nd you don’t need to configure them.

For IoT devices you need to first create a custom schema. You can send any kind of data with IoT devices.
It is recommended that you generate IoT schema based on your event payload. 
Once your schema is defined you can use it in a Channel and see data being ingested. 
You can update a schema if necessary at a later time.

## Creating a Schema

To create a schema through the dashboardL
1. Go to **Channels**
2. In the left sidebar under Schemas, click **+ Add Schema**

**SCREENSHOT**

3. **Name & Details** - Enter a name for the schema in "Name" field
4. Select **JSON** or **Query String** radio button depending on the schema you wish to create
5. **Define Fields** - Use this section to specify the format of your schema


Here is a simple example:


To create a schema using the API, 

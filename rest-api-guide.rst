SkyGrid REST API Reference
==========================

This document describes the REST API and resources provided by SkyGrid.
The REST APIs are for developers who want to build applications that
interface with SkyGrid, or connected devices which utilize the SkyGrid
platform.

This API is subject to change. SkyGrid will make all attempts to alert
customers to changes in the API ahead of time, but NO GUARANTEE IS MADE
that this version of the API will not change.

Skygrid's REST APIs provide access to resources (data entities) via URI
paths. To use a REST API, your application will make an HTTP request and
parse the response. The response format is JSON. Your methods will be
the standard HTTPS methods like GET, PUT, POST and DELETE. Because the
REST API is based on open standards, you can use any web development
language to access the API.

Structure of REST API queries
-----------------------------

With the exception of the login and logout endpoints, URIs for the
Skygrid REST API resource have the following structure:

::

    https://api.skygrid.io/domain/{identifier}/sub_domain/{sub-identifier}

The Skygrid API may require additional parameters in the URI request
headers and body.

Resources
=========

**Authentication**

-  `Login <#login>`__

**Devices**

-  `List <#list-devices>`__
-  `Fetch <#fetch-device>`__
-  `Add <#add-device>`__
-  `Update <#update-device>`__
-  `Delete <#delete-device>`__
-  `Fetch Device Properties <#fetch-device-properties>`__
-  `Update Device Properties <#update-device-properties>`__
-  `Fetch Device History <#device-history>`__

**Schemas**

-  `List <#list-schemas>`__
-  `Fetch <#fetch-schema>`__

--------------

Authentication
--------------

Login
~~~~~

Authenticate with Skygrid. Returns an authentication token (JWT) to be
used with furhter API requests. Tokens expire after 24 hours.

**Request**

``POST`` https://api.skygrid.io/login

**Header**


+----------------+--------------------+
| Parameter      | Value              |
+================+====================+
| Content-Type   | application/json   |
+----------------+--------------------+
| X-Project-Id   | PROJECT\_ID        |
+----------------+--------------------+

**Body**


::

    {
        "email": "user@emailaddress.com",
        "password": "secret_password",
    }

**Response**


``201`` - Created

::

    {
        "userId": "USER_ID",
        "token": "AUTH_TOKEN"
    }

``401`` - Unauthorized

--------------

Devices
-------

List Devices
~~~~~~~~~~~~

Returns a JSON list of all the devices for this project.

**Request**

``GET`` https://api.skygrid.io/devices

**Header**

+------------------+---------------+
| Parameter        | Value         |
+==================+===============+
| X-Access-Token   | AUTH\_TOKEN   |
+------------------+---------------+

**Response**

``200`` - OK

::

    [
        {
            "id": "urAA3btC",
            "schemaId": "Hsiph_ap",
            "name": "Waterpump",
            "properties": {
                "Status": "Off"
            },
            "log": false,
            "createdAt": "2016-08-31T03:20:10.796Z",
            "updatedAt": "2016-08-31T03:31:58.635Z"
        },
        {
            "id": "qMjNI0VR",
            "schemaId": "Windmill",
            "name": "Windmill",
            "properties": {
                "size": "63m",
                "status": "functioning"
            },
            "log": false,
            "createdAt": "2016-08-31T09:22:46.551Z",
            "updatedAt": "2016-08-31T09:22:46.551Z"
        }
    ]

``400`` - Bad Request

--------------

Fetch Device
~~~~~~~~~~~~

Fetch a specific device.

**Request**

``GET`` https://api.skygrid.io/devices/{device\_id}

**Header**

+------------------+--------------------+
| Parameter        | Value              |
+==================+====================+
| Content-Type     | application/json   |
+------------------+--------------------+
| X-Access-Token   | AUTH\_TOKEN        |
+------------------+--------------------+

**Response**

``200`` - OK

::

    {
        "id": "qMjNI0VR",
        "schemaId": "Hsiph_ap",
        "name": "newDevice",
        "properties": {
            "prop1": "Some property"
        },
        "log": false,
        "createdAt": "2016-08-31T09:22:46.551Z",
        "updatedAt": "2016-08-31T09:22:46.551Z"
    }

``400`` - Bad Request

``404`` - Not Found

--------------

Add Device
~~~~~~~~~~

Adds a new device to the project.

**Request**

``POST`` https://api.skygrid.io/devices

**Header**

+------------------+--------------------+
| Parameter        | Value              |
+==================+====================+
| Content-Type     | application/json   |
+------------------+--------------------+
| X-Access-Token   | AUTH\_TOKEN        |
+------------------+--------------------+

**Body**

::

    {
        "schemaId": "SCHEMA_ID",
        "name": "New Device Name"
    }

**Response**

``201`` - OK

::

    {
        "id": "NEW_DEVICE_ID"
    }

``400`` - Bad Request

--------------

Update Device
~~~~~~~~~~~~~

Update the name and properties of a device, and toggle logging for the
device.

``PUT`` https://api.skygrid.io/devices/{device\_id}

**Header**

+------------------+--------------------+
| Parameter        | Value              |
+==================+====================+
| Content-Type     | application/json   |
+------------------+--------------------+
| X-Access-Token   | AUTH\_TOKEN        |
+------------------+--------------------+

**Body**

+----------+------------------------------------------------+----------+---------+
| Paramete | Description                                    | Type     | Require |
| r        |                                                |          | d       |
+==========+================================================+==========+=========+
| name     | Device name                                    | String   | No      |
+----------+------------------------------------------------+----------+---------+
| properti | key/value pairs of properties describing the   | Dictiona | No      |
| es       | device state                                   | ry       |         |
+----------+------------------------------------------------+----------+---------+
| log      | Log device history                             | Boolean  | No      |
+----------+------------------------------------------------+----------+---------+

::

    {
        "name": "new_name",
        "properties": {
            "prop1": "new_property"
        },
        "log": true
    }

**Response**

``204`` - No Content Successfully updated.

``400`` - Bad Request

``404`` - Not Found

--------------

Delete Device
~~~~~~~~~~~~~

Delete a device.

**Request**

``DELETE`` https://api.skygrid.io/devices/{device\_id}

**Header**

+------------------+---------------+
| Parameter        | Value         |
+==================+===============+
| X-Access-Token   | AUTH\_TOKEN   |
+------------------+---------------+

**Response**

``204`` - No Content

``400`` - Bad Request

``404`` - Not Found

--------------

Fetch Device Properties
~~~~~~~~~~~~~~~~~~~~~~~

Returns the current state of a device.

**Request**

``GET`` https://api.skygrid.io/devices/{device\_key}/properties

**Header**

+------------------+---------------+
| Parameter        | Value         |
+==================+===============+
| X-Access-Token   | AUTH\_TOKEN   |
+------------------+---------------+

**Response**

``200`` - OK

::

    {
        "Distance": "0",
        "Velocity": "0",
        "Angle": ""
    }

``400`` - Bad Request

``404`` - Not Found

--------------

Update Device Properties
~~~~~~~~~~~~~~~~~~~~~~~~

Update the current state of the device

**Request**

``PUT`` https://api.skygrid.io/devices/{device\_id}/properties

**Header**

+------------------+--------------------+
| Parameter        | Value              |
+==================+====================+
| Content-Type     | application/json   |
+------------------+--------------------+
| X-Access-Token   | AUTH\_TOKEN        |
+------------------+--------------------+

**Body**

+-------------+-------------------------------------------------------------+--------------+------------+
| Parameter   | Description                                                 | Type         | Required   |
+=============+=============================================================+==============+============+
|             | key/value pairs of properties describing the device state   | Dictionary   | Yes        |
+-------------+-------------------------------------------------------------+--------------+------------+

::

    {
        "prop1": "someProp",
        "Hello": "world"
    }

**Response**

``204`` - No Content Successfully updated.

``400`` - Bad Request

``404`` - Not Found

--------------

History
~~~~~~~

Returns the history of a device's state.

**Request**

``GET`` https://api.skygrid.io/history/{device\_id}

**Header**

+------------------+---------------+
| Parameter        | Value         |
+==================+===============+
| X-Access-Token   | AUTH\_TOKEN   |
+------------------+---------------+

**Response**

``200`` - OK

::

    [
        {
            "time": "2016-10-03T06:07:17.207Z",
            "properties": {
                "Temp": "2",
                "Door_angle": "9001"
            }
        },
        {
            "time": "2016-10-03T06:07:29.502Z",
            "properties": {
                "Temp": "3",
                "Door_angle": "9001"
            }
        } ...
    ]

``400`` - Bad Request

``404`` - Not Found

--------------

Schemas
-------

List Schema
~~~~~~~~~~~

Returns a list of schemas in this project.

**Request**

``GET`` https://api.skygrid.io/schemas

**Header**

+------------------+---------------+
| Parameter        | Value         |
+==================+===============+
| X-Access-Token   | AUTH\_TOKEN   |
+------------------+---------------+

**Response**

``200`` - OK

::

    {
        "id": "Hsiph_ap",
        "name": "Streetsign",
        "properties": {
            "display": {
                "type": "string",
                "default": "Hi"
            }
        },
        "createdAt": "2016-08-28T09:33:00.657Z",
        "updatedAt": "2016-08-28T09:33:00.657Z"
    }

``400`` - Bad Request

``404`` - Not Found

--------------

Fetch Schema
~~~~~~~~~~~~

Fetch a specific schema.

**Request**

``GET`` https://api.skygrid.io/schemas/{schema\_id}

**Header**

+------------------+---------------+
| Parameter        | Value         |
+==================+===============+
| X-Access-Token   | AUTH\_TOKEN   |
+------------------+---------------+

**Response**

``200`` - OK

::

    {
        "id": "a-Yz8jBX",
        "name": "Vat2 Heat sensor",
        "properties": {
            "temperature": {
                "type": "number",
                "default": "312"
            }
        },
        "createdAt": "2016-08-28T09:33:50.830Z",
        "updatedAt": "2016-08-28T09:33:50.830Z"
    }

``400`` - Bad Request

``404`` - Not Found
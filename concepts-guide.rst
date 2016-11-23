Concepts Guide
_______________
This guide covers the basic concepts of the SkyGrid platform. To build IoT solutions in SkyGrid, it is important to understand how SkyGrid works.

.. _intro-sg:

============================
Introduction to SkyGrid
============================

SkyGrid is a platform for handling projects that require connecting up thousands of devices to the cloud for centralized management with an API for the physical world. SkyGrid makes development for the real world easy.

Whilst you may be familiar with other Internet of Things (IoT) platforms, Skygrid is different, it's more than just an IoT platform - it's an entire backend for your project. User and device management, fine grained security controls, data storage, and more can be managed by Skygrid.


SkyGrid provides the following:

* Device management, user management and security.
* Data visualization via the SkyGrid Data Explorer. 
* A simple to use API for connecting devices, users and applications with the SkyGrid platform. 
* Application SDK. A set of tools, libraries, relevant documentation, code samples that allow developers to create software applications on the SkyGrid platform. 
* Support for any hardware device capable of HTTPS. 
* Certified hardware development kits and software libraries for easy integration to SkyGrid. 


SkyGrid provides all the basic ingredients of an IoT project in a single environment, you can get started immediately without having to subscribe to various other services. It provides the complete development environment required by developers rolling out IoT solutions.

SkyGrid is a network agnostic platform and works over all network protocols including new LPWAN protocols such as SigFox and LoRa, as well as traditional WiFi and cellular.


.. _sg-concepts:

=================
SkyGrid Concepts
=================
First we will look at a typical IoT solution architecture, and the components involved. Then we describe the SkyGrid domain model.

.. image :: images/SkyGridPic1.PNG

-----------
Device
-----------
A device is the physical object which is connected to SkyGrid. The device pushes sensor data to the SkyGrid IoT platform. Devices can vary from connected cars to connected light bulbs. SkyGrid supports all hardware devices that can push data using HTTPS.

-----------
Device SDK
-----------
SkyGrid provides open source software libraries for the following devices: Raspberry Pi, Arduino, ESP32, C.H.I.P. Install the software on your device and instantly get connected to SkyGrid. SkyGrid will continuously update the device SDK repository to support new devices as they are released.

--------------------
SkyGrid IoT Platform
--------------------
Developers use the SkyGrid IoT Platform to build and host IoT solutions. The SkyGrid IoT platform manages all the devices in an IoT solution, via a simple to use administrative dashboard. It handles backend functionality, such as data storage and security. It allows data to be visualized in charts and maps using the SkyGrid Data Explorer. It handles user management for app development. The platform maintains a representation of the device at all times in its database. When apps query the device, they are querying the SkyGrid database. Most importantly, the SkyGrid platform implements a simple to use REST API for both devices and applications to use.

------------
Applications
------------
The sensor data stored in SkyGrid is used to power IoT apps. Applications can range from consumer mobile apps, to enterprise integration servers. An example of a simple application setup is an app running on a mobile browser. Another example is a web application server. Applications access SkyGrid via its API.

---------------
Application SDK
---------------
To extend the range of the SkyGrid API, and make for easier and faster application development, a powerful JavaScript SDK is provided. It includes support for web sockets, this means that SkyGrid powered apps can for example dynamically update data views, without requiring page reloads.

.. _sg-domain-model:

====================
SkyGrid Domain Model
====================
The SkyGrid domain model is based on the concept of projects, and it is important to understand how these are configured within SkyGrid.

.. image :: images/SkyGridConcepts.png

------------------
SkyGrid Admin User
------------------
A SkyGrid Admin user is the developer who logs into the SkyGrid platform and manages the complete project. The SkyGrid Admin user configures the projects, schemas, devices and project users.

-----------
Project
-----------
An IoT solution will typically have devices and app users. The easiest way to think of a project in SkyGrid, is that it is a way of grouping all these together. Devices and users are all configured on a per project basis in SkyGrid. Projects are assigned a unique Project ID, which is required in some API requests.

-----------
Schemas
-----------
The way to think of schemas is that they define a type of device. Schemas are created to describe the physical object they represent in the SkyGrid database. The schema for a weather station could for example be Temperature, Humidity and Device Serial number. There is no limit to the kind of schemas and devices SkyGrid can support. The schemas that define a device do not only have to reflect sensor data generated by that device, they can be used to tie other data to the device representation in SkyGrid. Schemas are assigned a Schema ID, which may be required in some API requests.

-----------
Devices
-----------
A device is created in SkyGrid by giving it a name, and a schema. Once a device is created in SkyGrid, it will be assigned a device key. The device must use this device key in any API requests towards SkyGrid (for example when the device is pushing sensor data to SkyGrid).

-----------
App User
-----------
A project can contain user details (email and password). These users are the end users of the IoT app. While they will never log directly into the SkyGrid administrative GUI, their account details are stored in a SkyGrid project. An app user is generally an end user for the created IoT app.

.. _device-perms:

==================
Device Permissions
==================
A device has its own access control permissions. Create, Read and Delete permissions for each device can be configured. Device permissions can be configured per user. Different users can obtain different permissions on each device. A device could be accessed by all project users, some project users, or as is more often the case, a device is linked to one project user. By default, the SkyGrid Admin user can access all devices.

.. _api-usage:

===========
API Usage
===========
The SkyGrid REST API is simple to use, here are some guidelines. API requests require a valid access token. Obtaining an access token requires a username and password be provided in the initial API request. The username and password belong to either an App user, or the SkyGrid Admin user. The access token returned by SkyGrid is valid for 24 hours. It is used in all subsequent API requests. There is an exception to this rule. When devices use the API to access SkyGrid, only a device key is required in the API request, and the initial authorization is not required. When the device is instantiated in SkyGrid a unique device key is created. The device should then use this key in API requests.

The following diagrams show the difference between Application API access, and device API access.

.. figure :: images/AppAccess.png

   Application (App User + SkyGrid Admin User) API Access

.. figure :: images/DeviceAccess.png

	 Device API access

The REST API is a simple yet powerful tool to interact with Skygrid. More detailed information can be found in the REST API Documentation found here.


.. _devices:

===========
Devices
===========
SkyGrid can support any hardware device that can send HTTPS requests. SkyGrid provides software libraries for the following devices to get you up and running quickly. 

+---------------------------------+------------------------------+-------------------------------+----------------------------+
| .. image :: images/arduino.jpeg | .. image :: images/chip2.png | .. image :: images/esp321.png | .. image :: images/Pi.jpeg |
+---------------------------------+------------------------------+-------------------------------+----------------------------+
| Arduino SDK                     | C.H.I.P. SDK                 | ESP32 SDK                     | Raspberry Pi SDK           |
+---------------------------------+------------------------------+-------------------------------+----------------------------+


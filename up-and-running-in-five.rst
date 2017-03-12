Quickstart
==========

A quick guide to using the SkyGrid Javascript SDK in a node environment

This guide will cover \* adding a device and it's schema to the platform
\* pushing data to the platform \* pulling data

Alright here's the situation. you've got a two devices with Temperature
sensors and you would like to use skygrid to manage the data.

Definitions
-----------

First things First
------------------

Signup for an account at
`dashboard.skygrid.io <https://dashboard.skygrid.io>`__, After
activating your account you should be able to login.

install the sdk in your project directory ``npm install skygrid``

The Project
-----------

You will need to create a project within the platform. everything in
skygrid revolves around projects.

    IMAGE location of create project link

    IMAGE creating the project

after you've done that take note of the project id and the master key.
The rest of the guide will be completed using the JavaScript SDK and
Postman.

adding a schema and then the devices
------------------------------------

On the platform a device represents each physical device that will use
the platform to host the data that it collects. To correctly verify the
data the platform recieves each device needs to have a schema associated
to it. So the schema has to be created first. (You can read more about
it `here <./concepts-guide.md>`__).

But before that you must instantiate the sdk and login to the platform.

::

    var SkyGrid = require("skygrid");

    // initiate the sdk with the project id
    // each instance of the sdk revolves around a project
    var project = new SkyGrid.project("bWChK69S");

    //some variables you're gonna need later on
    var schema1, dev1, dev2;

    // use the master key to grant this session master (administrator) priviledges
    project.loginMaster("1c6Ld7dGyPIe22p6cU37oBZl")

Now that you've logged on with master priviledges, you are free to
create/edit/delete anything on the platform. This is why you should be
careful who you give the master key to.

Now lets add a schema to the project. the schema defines the nature of
the data that is sent to the platform. this means you must provide a
list of properties defining it's type, name and if needed it's default
value.

::

        .then(() => {                   //all the functions are done within the then
                                        // calls of the promise since next to all
                                        // functions in the sdk return promises
            return project.addSchema(   //addSchema takes the following arguments
                "schema1",              //  name of the schema
                {                       //  a set of key-value pairs defining
                                        //  the properties of the schema
                    "temp":             //    each key is the name of the property
                    {
                        type: "number", //    each value defines the type
                                        //    (string,number,boolean) of the property
                        default: 0      //    and it's default value
                    },
                    "lat": {
                        type: "number"
                    },
                    "lng": {
                        type: "number"
                    }
                }
            )
        })

With the schema successfully created, you can use it to add your two
devices to the platform.

::

        .then(schema => {               // the schema object is returned after
                                        //  successfully adding the schema
            console.log("created schema with id:" + schema.id);
            schema1 = schema            //variable to keep track of this device

            return project.addDevice(   //addDevice takes the following arguments
                "dev1",                 // name of the device
                schema1                 // schema that the device will use
            );
        })
        .then(device => {
            console.log("created device with id:" + device.id);
            dev1 = device;
            return project.addDevice(
                "dev2",
                schema1
            );
        })

Something else you probably would like to do is to turn on logging for
these devices. This way the platform will keep track of all the data it
recieves from these devices instead of just it's current state.

::

        .then(device => {
            console.log("created device with id:" + device.id);
            dev2 = device;

            //enable logging of data in both devices
            dev1.log = true;
            dev2.log = true;

            //saves the changes made to both devices (the enabling of logging)
            return dev1.save().then(() => dev2.save);
        })

    Note: this could've been done via the dashboard but what kind of
    guide would this be then.

Pushing data
------------

At this point you should be able to see the new devices and schema on
the dashboard

    IMAGE with devices and schemas on the dashboard

New data can be added in two ways: \* changing properties with the SDK
\* a PUT request with postman (or curl, etc) / with a https library in
any programming language you prefer

a dissection of the PUT request
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  Method : PUT
-  endpoint : https://api.skygrid.io/devices/{Device id}/properties
-  headers
-  X-Project-Id : {Project id}
-  Content-Type : application/json
-  body : an object of key-value pairs, key is the property name and the
   value is the value you want to set for this property

Some examples:
~~~~~~~~~~~~~~

-  Postman : > Image using postman

-  SDK :

::

    var project = new SkyGrid.project("bWChK69S");
    dev = project.device(**************Complete according to screenshots are taken********);

    dev.set("temp",33.0);
    dev.set("lat",-33.917465);
    dev.set("lng",151.230863);
    dev.save().then(()=>process.exit(0));

    this is the part that needs to be implemented in each of your
    devices

Viewing data
------------

You should be able to see the new data on the dashboard, but this is
probably not what you want to show your users.

The most simple method of viewing data from the platform is to go
through all the devices and print it's properties

::

    var project = new SkyGrid.project("bWChK69S");
    project.devices()           //also returns a promise
        .then(devices => {      //it resolves to a list of devices currently
                                //  managed by the platform
            devices.forEach(device => {
                console.log("=================");
                console.log("getting details of device "+ device.name);
                console.log("temperature: " + device.get("temp") + " C");
                console.log("current location: " + device.get("lat") +
                            ", " + device.get("lng"));
            });
        })

But a more common and suitable method is to subscribe either to the
project or to a specific device to be notified of new properties

::

    var project = new SkyGrid.project("bWChK69S");
    printDev = device => {
        console.log("getting details of device "+ device.name);
        console.log("temperature: " + device.get("temp") + " C");
        console.log("current location: " + device.get("lat") + ", " + device.get("lng"))
    }

    project.subscribe(device => {
        console.log("~~~ project listener ~~~")
        printDev(device)
    })

    project.device("****Complete after getting screenshots*******").subscribe(device => {
        console.log("~~~ device listener ~~~")
        printDev(device)
    })

    CODE output of both files

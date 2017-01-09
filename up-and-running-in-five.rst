Up and Running in 5 Minutes
____________________________

This guide is intended to be a 'hit the ground running' guide on using SkyGrid. We recommend first reading the :doc:`concepts-guide`.


======================
Register a new account
======================
First head across to https://dashboard.SkyGrid.io/, and select 'Register a new account' A confirmation email will be sent to the email address you've provided. Click on the link provided in that email, which verifies your account. After verification, click to go back to the login page and log in to the dashboard with the registration email and password you previously provided. Congratulations, you are now a SkyGrid Admin user.


=======================================
Create a new project and app user
=======================================
The first page you are greeted to is the Projects page. Right now this is empty, so let's fix that! In the top right corner click 'Create New Project' For this first example, let's just call the project 'Demo Project' We need to create an App User for this project - App Users are different from SkyGrid Admin users. SkyGrid Admin users have access to the SkyGrid dashboard and can make administrative changes to all the projects in an account. App Users are the users who will be using the IoT you create. For now just register an app user account using the same email you signed up with (we recommend using a different password).

In the projects panel now you can see 'Demo Project' has been created. The ID for this project is on the left.

.. image :: images/proj1.PNG


======================
Create a schema
======================
The way to think of schemas is that they define a type of device. One simple example would be a weather station: it monitors temperature, humidity, and the serial number of the device (so that we have a string parameter we can modify) Click 'create a schema' and fill in the name as 'Weather station 1'. Click "Add new property" to add the first parameter: temperature. Then add parameters that define humidity and the serial number.

.. image :: images/sk1.PNG


======================
Instantiate a Device
======================
In the left hand panel you can see your device types: click the one we just created 'Weather station 1' and it will take you to the schema description page. Let"s go ahead and make some weather stations! Click the "Add a new device" button below the schemas, and the following window will pop up.

.. image :: images/newdev.PNG

Go ahead and make a few different weather stations.

You can click on one of the devices listed in the sidebar, and it will take you to the information page for this device.

.. image :: images/dev_screen1.PNG

Take note of the device Key - we shall use it in the next section

======================
Turn on logging
======================
In the devices panel, turn on logging for this device

.. image :: images/logging.PNG

======================
Push some data
======================

Now we have created a device in SkyGrid, we can push data to SkyGrid. Normally, this would be done by coding a device. Instead, we are going to simulate the device pushing up data by using a program called `Postman`_, which is used to interact with the REST API. More detail on how to construct queries can be found here - to perform just one device update though, set the following parameters:

.. _Postman: https://www.getpostman.com

* Copy the API endpoint into the PUT field https://api.SkyGrid.io/d/your_device_key_here
* In the Headers field, set one of the headers to Content-Type - application/json
	.. image :: images/postman1.PNG
* In the body of the request, choose 'raw' for encoding type and then copy paste the following JSON into the text block

::

	{
		"temperature": 327,
		"humidity": 12
	}

.. image :: images/postman2.PNG

To see the data being pushed to SkyGrid, click on the corresponding device in the left hand panel under devices. The Current State will be updated, as will the Event Log.

Try sending some different values from the device to SkyGrid. Then go to 'Data explorer' in the left menu bar. Select 'Weather station 1' as the schema, the device which we are pushing data to, the fields we are updating and the time that we wish to observe the data over (probably just the last few minutes).

.. image :: images/explorer.PNG


=======================================
Example: Simple application
=======================================

The other side of your project is the webpage, software or application which needs to read and write SkyGrid data. There are obviously a hundred different ways this can be done: we shall be creating a simple webpage that pulls down and displays the data from your project.

:download:`Download this HTML file <downloads/simple-app.html>` and open it in your favorite text editor. Note, save the page as type "Webpage, HTML Only" (and not as type "WebPage, Complete").

On line 96 of the HTML file, edit the code to include your own project ID

::

	// Create a new SkyGrid project object
	var project = SkyGrid.project('aFFzyVKi');

Once that has been set, open the HTML file in your web browser and use Postman to push some new data to SkyGrid.

.. image :: images/app1.PNG

New data will appear in real time, and a satisfying little animation takes place. In this sample app, the app is configured to listen to the "project", so if any device in that project pushes data to SkyGrid, the animation will take place.

That's it! In (hopefully) under 5 minutes you have created a simple but complete, end to end IoT application. See further guides for more detailed information on using SkyGrid.
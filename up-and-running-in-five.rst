Up and Running in 5 Minutes
____________________________

This guide is intended to be a 'hit the ground running' guide on using Skygrid. We recommend first reading the :doc:`concepts-guide`.


======================
Register a new account
======================
First head across to https://dashboard.skygrid.io/, and select 'Register a new account' A confirmation email will be sent to the email address you've provided. Click on the link provided in that email, which verifies your account. After verification, click to go back to the login page and log in to the dashboard with the registration email and password you previously provided. Congratulations, you are now a SkyGrid Admin user.


=======================================
Create a new project and app user
=======================================
The first page you are greeted to is the Projects page right now this is empty, so let's fix that! In the top right corner click 'Create New Project' For this first example, let's just call the project 'Demo Project' We need to create an App User for this project - App Users are different from Skygrid Admin users. SkyGrid Admin users have access to the Skygrid dashboard and can make administrative changes to all the projects in an account. App Users are the users who will be using the IoT you create. For now just register an app user account using the same email you signed up with (we recommend using a different password).

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

Now we have created a device in Skygrid, we can push data to SkyGrid. Normally, this would be done by coding a device. Instead, we are going to simulate the device pushing up data by using a program called `Postman <https://www.getpostman.com/>`, which is used to interact with the REST API. More detail on how to construct queries can be found here - to perform just one device update though, set the following parameters:

* Copy the API endpoint into the POST field https://api.skygrid.io/d/your_device_key_here
* In the Headers field, set one of the headers to Content-Type - application/json
	.. image :: images/postman1.PNG
* In the body of the request, choose 'raw' for encoding type and then copy paste the following JSON into the text block

::

		{
	   "temperature": "327",
	   "humidity": "12"
	  }

.. image :: images/postman2.PNG

To see the data being pushed to SkyGrid, click on the corresponding device in the left hand panel under devices. The Current State will be updated, as will the Event Log.

Try sending some different values from the device to SkyGrid. Then go to 'Data explorer' in the left menu bar. Select 'Weather station 1' as the schema, the device which we are pushing data to, the fields we are updating and the time that we wish to observe the data over (probably just the last few minutes).

.. image :: images/explorer.PNG


=======================================
Example: Simple application
=======================================

The other side of your project is the webpage, software or application which needs to read and write Skygrid data. There are obviously a hundred different ways this can be done: we shall be creating a simple webpage that pulls down and displays the data from your project.

:download:`Download this HTML file <downloads/simple-app.html>` and open it in your favorite text editor. Note, save the page as type "Webpage, HTML Only" (and not as type "WebPage, Complete").

On line 94 of the HTML file, edit the code to include your own project ID

.. image :: images/edit1.PNG

Once that has been set, open the HTML file in your web browser and use Postman to push some new data to Skygrid.

.. image :: images/app1.PNG

New data will appear in real time, and a satisfying little animation takes place. In this sample app, the app is configured to listen to the "project", so if any device in that project pushes data to SkyGrid, the animation will take place.

That's it! In (hopefully) under 5 minutes you have created a simple but complete, end to end IoT application. See further guides for more detailed information on using Skygrid.

=======================================
Example: Simple application++
=======================================
Above, we created a simple HTML page that displayed some data. In this section we will go through this basic example in more detail.

:download:`Open up this HTML file <downloads/simple-app2.html>` in your favourite text editor

Let's go through the code.

Lines 1-56 are related to the Sylesheet for the page, and including some Javascript libraries that we need. We won't go into too much detail about this, if you're completely unfamiliar with HTML and CSS then perhaps `this <https://www.khanacademy.org/computing/computer-programming/html-css>` page may be informative for you.

We're only concerned with one line in this section, line 10. This line imports the Skygrid Javascript SDK from our server. You can download and include this locally as well, but for now we'll just import it from the internet.::
  <!-- Skygrid sdk -->
	<script src="https://cdn.skygrid.io/sdk/js/skygrid-latest.min.js"></script>

Lines 57 - 85 are normal HTML, they contain the actual content of the webpage. We can see on line 61 we've declared the Sun icon to exist. Below that is a span with an id="temp" - we will update this field with some Javascript in a bit. Line 76 has something similar, an element with id="humidity" - again, we shall use the Skygrid SDK to pull data from Skygrid and update these fields with the values we pull down.::

  <body>
	<center>
	  <h1 style="margin-top: 50px" style="margin-bottom: 50px">Skygrid weather app</h1>
	  <div class="details">
	    <div class="container" style="margin-bottom: 50px"><i id="sun" class="fa fa-sun-o red big "></i></div>
	    <span id="temp">...</span> &deg;Cs
	  </div>

	  <hr style="height: 40px; border: none;">

	  <div class="" style="margin-top: 50px">
	    <table style="width: 550px;">
	      <tr>
	        <td>
	          Humidity
	        </td>
	      </tr>
	      <tr>
	        <td>
	          <div class="sub_details" id="humidity">...</div>
	        </td>
	      </tr>


	      <tr><td colspan="2" style="height: 50px;"></td></tr>

	    </table>
	  </div>
	</center>

All the Javascript code that interacts with Skygrid is contained within lines 87 to 104::

	<!-- Hit Skygrid to get weather station data -->
	<script>
	function log(message) {
	  //element.innerHTML += message + '<br />';
	  console.log(message);
	}

	let client = new SkyGrid('YCZkQ0tD');
	client.subscribe(function(device, changes) {

	  $("#temp").text(device.get('temperature'));
	  $("#sun").stop().animate({"font-size" : "260px"}).delay(50).animate({"font-size" : "240px"});

	  $("#humidity").text(device.get('humidity'));
	  $("#serial").text(device.get('serial'));

	});
	</script>

This section contains all the Javascript running on the webpage. Let's step through it in sections.

The first function in the script section is a simple logging function. Modern browsers all have a console that pages can write to using console.log(). You can see this console by right clicking a page and choosing 'inspect'.

The next lines create a Skygrid object, and set up a subscribe function using that object. The code to create new Skygrid objects was imported via the SDK at the top of the file. This object provides a convenient interface for the programmer to interact with Skygrid. Here we need to change something - enter the project ID for the project you wish to subscribe to where you create the Skygrid object i.e. replace 'your_project_id_here' with your project id, still inside the single quotes. For example::

	let client = new SkyGrid('YCZkQ0tD');

NOTE: If your application doesn't appear to be working, you can open up the debugging console and see if you're getting any errors. In Chrome, you can open this with 'Ctrl+Shift+I', in Firefox it is 'Ctrl+Shift"Q'. CLick on 'Console' and see if there is an error - if you are receiving the following error, then you have not successfully saved the HTML file with your correct project ID.

.. image :: images/error1.PNG

When subscribe is called, any time a piece of data in the project changes, the function that we pass it in as an argument will be called - you must pass subscribe a function with two arguments, device and changes.

The contents of the function we pass into subscribe will be called every time a piece of data in this project changes. This allows us to update all the values on the page, and trigger simple events such as the animation. This line::
	$("#humidity").text(device.get('humidity'));

Is a call to the Javascript library called JQuery (which we also imported, you can see so at the top of the file). This line searches the HTMl for the element with id="humidity" and changes the text in this element to whatever we pass that function. In this case, we wish to change the text to the latest value for humidity, which we get from SKygrid using the function device.get(). The parameter we wish to use must match the parameters as described in the schema.

The SDK is very powerful, and can do much more than what we've talked about here. Check out the Skygrid :doc:`javascript-sdk-guide` for more information.

======================
Using the REST API
======================

The REST API is the most basic way to interact with the Skygrid platform. It is very flexible, as any language capable of creating HTTP requests can use the REST API. It is very simple and easy to get started. For a complete reference, see the :doc:`rest-api-guide`.

To use the REST API, most queries require an authentication token. The endpoint we use to aquire a token is the Login endpoint. The following screenshots from Postman show how to set up the request:

.. image :: images/login1.PNG

The Content-Type will always be application/json. Simply change x-project-id to your project ID, which can be found in the Skygrid dashboard.

.. image :: images/login2.PNG

In the body section, replace the details above with the details of your *project user details* - these are not necessarily the same that you use to log in to the Skygrid dashboard! These are the details for a user which has been added to this particular project. Remember the Skygrid dashboard user can be administering multiple projects, but project users only have access to that specific project.

If login is successful, you should get a response similar to that below, containing the authentication token.

.. image :: images/login3.PNG

This token is required for most queries performed through the REST API. Next up, we will use this token to query a Skygrid project for a list of devices.

=======================================
Example: Getting a list of Devices
=======================================
We shall begin by querying to get a list of Schemas. This requires a project-ID, but does not require an authentication token in the header. The URL is:

https://api.skygrid.io/schemas

With the project ID passed in via the header, with the header tag *x-project-id*

.. image :: images/schema1.PNG

A JSON object containing all the information for schemas is returned. This can be parsed by any standard JSON parsing code library.

To get a list of devices in a project, we can use a different endpoint

https://api.skygrid.io/devices

With the project ID passed in via the header, with the header tag *x-project-id*

.. image :: images/devices1.PNG

Once again, a JSON object is returned containing all the information from the request.

The REST API is a simple yet powerful tool to interact with Skygrid. More detailed information can be found in the :doc:`rest-api-guide`.

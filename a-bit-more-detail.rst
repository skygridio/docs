Introduction to SkyGrid web apps
________________________________

In this guide we shall go into the simple_app.html application with a bit more depth, explaining exactly what each section is doing.

=======================================
Deep dive: simple_app.html
=======================================

Above, we created a simple HTML page that displayed some data. In this section we will go through this basic example in more detail.

:download:`Download this HTML file <downloads/simple-app.html>` and open it in your favourite text editor

Let's go through the code.

Lines 1-56 are related to the Stylesheet for the page, and including some Javascript libraries that we need. We won't go into too much detail about this, if you're completely unfamiliar with HTML and CSS then perhaps `this page`_ may be informative for you.

.. _this page: https://www.khanacademy.org/computing/computer-programming/html-css

We're only concerned with one line in this section, line 10. This line imports the SkyGrid Javascript SDK from our server. You can download and include this locally as well, but for now we'll just import it from the internet.

::

	<!-- SkyGrid sdk -->
	<script src="https://cdn.SkyGrid.io/sdk/js/SkyGrid-latest.min.js"></script>

Lines 57 - 85 are normal HTML, they contain the actual content of the webpage. We can see on line 61 we've declared the Sun icon to exist. Below that is a span with an id="temp" - we will update this field with some Javascript in a bit. Line 76 has something similar, an element with id="humidity" - again, we shall use the SkyGrid SDK to pull data from SkyGrid and update these fields with the values we pull down.

::

  <body>
	<center>
	  <h1 style="margin-top: 50px" style="margin-bottom: 50px">SkyGrid weather app</h1>
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

All the Javascript code that interacts with SkyGrid is contained within lines 87 to 120

::

    <!-- Hit SkyGrid to get weather station data -->
    <script>
    function log(message) {
      //element.innerHTML += message + '<br />';
      console.log(message);
    }


    // Create a new SkyGrid project object
    var project = SkyGrid.project('');
    // Use the project object to fetch a device object
    var myDevice = project.device('7VcOapOH').fetch().then((device) => {
      
      // Apply a subscription to any changes (updated in real-time via websockets)
      device.subscribe(function(changes) {
        // When a change comes through, update the page
        update(device);
      });

      // Apply first update to the page
      update(device);

    });


    function update(device) {
      // Update 
      $("#temp").text(device.get('temperature'));
      $("#sun").stop().animate({"font-size" : "260px"}).delay(50).animate({"font-size" : "240px"});

      $("#humidity").text(device.get('humidity'));
      $("#serial").text(device.get('serial'));
      
    };

This section contains all the Javascript running on the webpage. Let's step through it in sections.

The first function in the script section is a simple logging function. Modern browsers all have a console that pages can write to using console.log(). You can see this console by right clicking a page and choosing 'inspect'.

The next lines show how to set up a device subscription, so that when the data for a device changes (like when a device in the field pushes an update) the webpage will automatically be updated. SkyGrid is organised around projects, so first we get the project object corresponding to a particular project id. The code to create new SkyGrid objects like this was imported via the SDK at the top of the file. Objects like this are a convenient interface for the programmer to interact with SkyGrid.

Devices are attached to projects, so we can expect to access devices via a project.

For example

::

    // Create a new SkyGrid project object
    var project = SkyGrid.project('');
    // Use the project object to fetch a device object
    var myDevice = project.device('7VcOapOH').fetch().then((device) => {
      
      // Apply a subscription to any changes (updated in real-time via websockets)
      device.subscribe(function(changes) {
        // When a change comes through, update the page
        update(device);
      });

NOTE: If your application doesn't appear to be working, you can open up the debugging console and see if you're getting any errors. In Chrome, you can open this with 'Ctrl+Shift+I', in Firefox it is 'Ctrl+Shift"Q'. Click on 'Console' and see if there is an error - if you are receiving the following error, then you have not successfully saved the HTML file with your correct project ID.

.. image :: images/error1.PNG

When subscribe is called, any time a piece of data in the project changes, the function that we pass it in as an argument will be called - you must pass subscribe a function with two arguments, device and changes.

The contents of the function we pass into subscribe will be called every time a piece of data in this project changes. This allows us to update all the values on the page, and trigger simple events such as the animation. This line::

	$("#humidity").text(device.get('humidity'));

Is a call to the Javascript library called JQuery (which we also imported, you can see so at the top of the file). This line searches the HTMl for the element with id="humidity" and changes the text in this element to whatever we pass that function. In this case, we wish to change the text to the latest value for humidity, which we get from SkyGrid using the function device.get(). The parameter we wish to use must match the parameters as described in the schema.

The SDK is very powerful, and can do much more than what we've talked about here. Check out the SkyGrid :doc:`javascript-sdk-guide` for more information.

======================
Using the REST API
======================

The REST API is the most basic way to interact with the SkyGrid platform. It is very flexible, as any language capable of creating HTTP requests can use the REST API. It is very simple and easy to get started. For a complete reference, see the :doc:`rest-api-guide`.

To use the REST API, most queries require an authentication token. The endpoint we use to aquire a token is the Login endpoint. The following screenshots from Postman show how to set up the request:

.. image :: images/login1.PNG

The Content-Type will always be application/json. Simply change x-project-id to your project ID, which can be found in the SkyGrid dashboard.

.. image :: images/login2.PNG

In the body section, replace the details above with the details of your *project user details* - these are not necessarily the same that you use to log in to the SkyGrid dashboard! These are the details for a user which has been added to this particular project. Remember the SkyGrid dashboard user can be administering multiple projects, but project users only have access to that specific project.

If login is successful, you should get a response similar to that below, containing the authentication token.

.. image :: images/login3.PNG

This token is required for most queries performed through the REST API. Next up, we will use this token to query a SkyGrid project for a list of devices.

=======================================
Example: Getting a list of Devices
=======================================
We shall begin by querying to get a list of Schemas. This requires a project-ID, but does not require an authentication token in the header. The URL is:

https://api.SkyGrid.io/schemas

With the project ID passed in via the header, with the header tag *x-project-id*

.. image :: images/schema1.PNG

A JSON object containing all the information for schemas is returned. This can be parsed by any standard JSON parsing code library.

To get a list of devices in a project, we can use a different endpoint

https://api.SkyGrid.io/devices

With the project ID passed in via the header, with the header tag *x-project-id*

.. image :: images/devices1.PNG

Once again, a JSON object is returned containing all the information from the request.

The REST API is a simple yet powerful tool to interact with SkyGrid. More detailed information can be found in the :doc:`rest-api-guide`.

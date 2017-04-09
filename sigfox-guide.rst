Linking devices to SkyGrid using the SigFox network
___________________________________________________

Sigfox is a proprietary LPWAN solution with network operators building infrastructure around the globe. In this guide, we shall show how to connect to the SigFox network operated throughout New Zealand, Australia and Hong Kong - Thinxtra_. 

===========================
Your first SigFox device
===========================

This tutorial we shall use the SiPy_ development board. As the name suggest, SiPy can be programmed in Python. It supports SigFox, Wifi and BlueTooth network procols. PyCom provide extensive documentation_ on how to program these boards. With up to 24 GPIO, 8 ADC inputs, UART, SPI, I2C etc. support, this module is targetted at enterprise users with large deployments, but it's still very simple to use and is a good starting module.

After following the above guides, run this python script to get the SigFox ID and PAC, which you will need to register the device with Thinxtra. Once registered, sending data through the SigFox network requires a few lines of simple python. Note that the sending code is commented out by default.

.. code:: python
	
	from network import Sigfox
	import binascii
	import socket
	 


	##
	## init Sigfox for RCZ4 (Austrlia/NZ)
	##

	sigfox = Sigfox(mode=Sigfox.SIGFOX, rcz=Sigfox.RCZ4)
	binascii.hexlify(sigfox.id())
	binascii.hexlify(sigfox.pac())



	##
	## Send a Sigfox message
	##

	# create a Sigfox socket
	#s = socket.socket(socket.AF_SIGFOX, socket.SOCK_RAW)
	 
	# make the socket blocking
	#s.setblocking(True)
	 
	# configure it as uplink only
	#s.setsockopt(socket.SOL_SIGFOX, socket.SO_RX, False)
	 
	# send some bytes
	#s.send(bytes([1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12]))


==========================================
Adding SigFox devices to a SkyGrid project
==========================================

The SIGFOX network allows your device to send up to 140 messages per day.
Each message can be up to 12 bytes of actual payload data. You can also
transmit 4 messages to each device per day, these download messages can be
up to 8 bytes. For a device to receive messages, it must send a request to the
Sigfox backend, which means that the device must be programmed to receive
data at certain times, or when certain device conditions occur.
The Sigfox protocol also transmits the Sigfox device ID in the message, the 12
and 8 bytes represent the actual payload. Sigfox does not understand or
transform your payload, only you know how it is structured. You can structure
the payload as required by your application.
12 bytes easily covers the needs for devices that transmit data such as the
location co-ordinates, other sensors measurements or alarm events. The
downlink ability allows you to send 8 bytes of data to the device, which can be
used as configuration data if needed. You can improve battery life by only using
uplink, if you do not need two-way communications.

How the sigfox integration works
********************************

Sigfox offers a callback service: every time a message is received by the
network, you can set up the way you want it to be forwarded to SkyGrid. This
lets you take advantage of the SkyGrid features such as our powerful SDKs, data
visualization, integration to other systems, and all the other benefits that come
with the SkyGrid IoT platform.
In the Sigfox backend, the configuration of callbacks is done per device type,
and is configured within the device type page. When a device uploads a message
to the Sigfox backend, it will trigger the callback towards SkyGrid.
A callback has a set of common variables:
Device Id : device identifier (in hexadecimal – up to 8 characters - 4 bytes)
Data : the user data (in hexadecimal, max 12/8 bytes, upload/download)
Others : time, duplicate, snr, station, avgSnr, lat, lng, rssi, seqNumber


1 Register the SkyGrid dev kit on the Sigfox backend
****************************************************

Select your country/operator (Australia and New Zealand use Thinxtra). Enter the SigFox ID and PAC you collected from the Sigfox device at the start of the tutorial.

Enter some more information including name and email, you will then receive
an email from Sigfox with your account details. Use this to log into the
Sigfox backend.

2 Add a new schema in SkyGrid
*****************************

The schema properties must align with how you will construct your payload
on the device. You must specify the length of each property for Sigfox
properties within the schema.
Total length of the Sigfox properties cannot exceed 12 bytes.

3 Create a Sigfox device in SkyGrid
***********************************

Create a new device, of the schema configured in the previous step.
Select Sigfox for connectivity.
Enter the PAC and ID.

4 Get the callback URL
**********************

Once you have created a Sigfox device, click on the Sigfox Settings to get
the callback information that you must use to configure the Sigfox backend.
You will see the details below, for configuring the Sigfox backend in the
next step.
This URL only gets those values you have configured in SkyGrid. If you
need other values like time, duplicate, snr, station, data, avgSnr, lat, lng,
rssi and seqNumber; you must include a key value for each one at the url
query. For more information on how to configure custom calls see the
`sigfox documentation <https://blog.sigfox.com/create-callback-sigfox-backend/>`_.


5 Configure the callback in the Sigfox backend
**********************************************

Go to the SkyGrid-Radiocrafts Device Type on the Sigfox back end, and click
edit.
Select Callback and click New.
Click Custom Callback.
Insert the values provided by SkyGrid in the previous step.
Click OK to save the callback Url.
6 Upload data to Sigfox, and view in SkyGrid
Push data from your device to the Sigfox backend, use SkyGrid data explorer
to view the data.


.. _Thinxtra: http://www.thinxtra.com/
.. _SiPy: https://www.pycom.io/product/sipy/
.. _documentation: https://docs.pycom.io/pycom_esp32/index.html 


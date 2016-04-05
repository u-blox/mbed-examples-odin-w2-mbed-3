# udp-time-client-wifi
Example to show how to use Wi-Fi on the ODIN-W2 for ARM mbed.

The application udp-time-client-wifi will demonstrate how to:

* Initialize WiFi driver
* Start WiFi driver
* Connect WiFi client with WPA2 security to an access point
* Start LWIP
* Get IP address with DHCP
* Resolve time server host name
* Get time from time server
* Output the given time on debug UART

## Required hardware
* EVK-ODIN-W262 development board for the u-blox ODIN W2 module
* USB micro cable
* Access Point with internet connection


## Required software
* yotta [install instructions here](http://yottadocs.mbed.com/)

## Get the source code
### Example source code
git clone https://github.com/u-blox/mbed-examples-odin-w2.git 
### Depending modules 
git clone https://github.com/u-blox/ublox-odin-w2-drivers-binary.git  
git clone https://github.com/u-blox/ublox-odin-w2-lwip-adapt.git  
git clone https://github.com/u-blox/sal-stack-lwip-ublox-odin-w2.git  
git clone https://github.com/u-blox/target-ublox-odin-w2-gcc.git

## Configuring and building with Yotta
Yotta is used to for building this example. 
Before building you need to do some configuration.
### Create yotta links for depending modules  
cd target-ublox-odin-w2-gcc  
yotta link-target  
cd..  

cd ublox-odin-w2-lwip-adapt  
yotta link  
cd..  

cd sal-stack-lwip-ublox-odin-w2  
yotta link  
cd..  

cd ublox-odin-w2-drivers-binary   
yotta link  
cd .. 

### Link your project to depending modules
cd mbed-examples-odin-w2  
cd udp-time-client-wifi  
yotta link ublox-odin-w2-lwip-adapt  
yotta link sal-stack-lwip-ublox-odin-w2   
yotta link ublox-odin-w2-drivers   
 
### Configure your target platform  
yotta link-target ublox-odin-w2-gcc  
yotta target ublox-c029-gcc

### Preparing configuration file
A configuration file is needed to be able to build this example application.   
Create the file "config.json" and place it the root folder of your project.   

Include the following to your config.json
```json
{
"test": {
    "wifi_ssid": "\"YOUR AP\"",
    "wifi_passphrase": "\"YOUR PASSWORD\"",
    "bt_remote_nap_addr": "0x11, 0x22, 0xAA, 0xBB, 0xCC, 0x00"
  }
}
```

### Building the example
Use the following to build: yotta build --config config.json

If build is successful you will find the resulting binary in the following location   
\mbed-examples-odin-w2\udp-time-client-wifi\build\ublox-c029-gcc\source\mbed-example-odin-w2-wifi.bin

## Running the application

To program the application to flash on your EVK-ODIN-W262 just copy the application binary to your mbed USB mass storage device.
After successful programming reset your device and the application will start executing.   
To observe output from the application start a terminal application (e.g. Tera Term) and connect to the USB comport of the EVK-ODIN-W2. Baudrate settings are 9600, no flow control.

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
* EVK-ODIN-W26 development board for the u-blox ODIN W2 module
* USB micro cable
* Access Point with internet connection (or phone acting as a personal Hotspot)


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
Yotta is used to for building this example. Before building you need to do some configuration.   
The steps below are to be executed in the yotta console window.

### Create yotta links for depending modules  
cd target-ublox-odin-w2-gcc  
yotta link-target  
cd ..  

cd ublox-odin-w2-lwip-adapt  
yotta link  
cd ..  

cd sal-stack-lwip-ublox-odin-w2  
yotta link  
cd ..  

cd ublox-odin-w2-drivers-binary   
yotta link  
cd .. 

### Link your project to depending modules
cd mbed-examples-odin-w2  
cd udp-time-client-wifi  
yotta link ublox-odin-w2-lwip-adapt  
yotta link sal-stack-lwip-ublox-odin-w2   
yotta link ublox-odin-w2-drivers   
 
### Link and configure your target platform  
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
When you plugin your EVK-ODIN-W2 board to your computer it should turn up as a USB mass storage device. To program the application binary to your board simply copy and paste the application binary to your mbed USB mass storage device.  

After successful programming reset your device and the application will start executing.   

During executing some application status messages are printed to the degugging UART of the EVK-ODIN-W2.
To observe these status messages use a terminal application (e.g. Tera Term) and connect to the virtual comport that is provided over the USB interface.   

The virtual comport should present itself as "STMicroelectronics STLink Virtual COM Port" and you can check the port number to use in you device manager if you didn't observe it during installation.   
Baudrate settings are as follow:   

Baudrate: 9600   
Data: 8 bit   
Parity: none   
Stop: 1 bit   
Flow control: none   

##Debugging
###Example for Eclipse

1. Download and install Eclipse from http://www.eclipse.org/downloads/ 
 - Verified with Version: Luna Service Release 2 (4.4.2)   
   http://www.eclipse.org/downloads/packages/eclipse-ide-cc-developers/lunasr2
 - May require you to download Oracle JRE (verified with v1.8.0)
 - During installation select the option to install the "Eclipse IDE for C/C++ Developers"   
 
2. Install ARM plugins for Eclipse 
 - http://gnuarmeclipse.github.io/plugins/install/   
 The "Quick links" method descirbed on this page is recommended. However we experienced problem with this on some systems. If you encounter such problems then we suggest you to follow the "The local archive way" as described further down on the above mentioned page.   
 We verified this step with the following zip-archive:   
 https://github.com/gnuarmeclipse/plug-ins/releases/tag/v2.11.3-201602101653   


3. Install OpenOCD
 - Go to http://gnuarmeclipse.github.io/openocd/install/
 - Follow instruction for Windows.
 - Follow instructions to Update OpenOCD path in Eclipse.
 
4. Creating Eclipse project
 - In the yotta console execute the following command from the root folder of your project to create a project file for Eclipse:   
 yotta build --config config.json -G "Eclipse CDT4 - Ninja"

5. Importing project to Eclipse
 - Use "File/Import/General/Existing Projects into Workspace" to import the project file you just created
 - Select the root folder of you ARM mbed project.   
 - Note that many of the files are auto-generated with yotta and modifications might get overwritten.   
 - Note that building a project with "CDT Makefile Builder" will not detect any changes in .json-files. Add a yotta build instead, click Properties of the project and select "Builders" and "New".


6. Setup debugging in your Eclipse project
 - Right click on project in Project Explorer and select "Debug As.../ Debug Configurations..."
 - Select "GDB OpenOCD Debugging" to create new configuration for this project
 - Under the "Main" find "C/C++ Application" and select the ELF file for you project. The ELF is located under \build\"your target platform"\source\ and has the same name as you application binary, but is bigger. (e.g. C:\git\EVK-ODIN-W262_test\mbed-examples-odin-w2\udp-time-client-wifi\build\ublox-c029-gcc\source\ublox-odin-w2-drivers-test-udp-time-client-wifi)
 - Under the "Debugger" tab find "OpenOCD Setup/Config Options" and copy paste the following   
  -f "${openocd_path}\..\scripts\interface\stlink-v2-1.cfg" -f "${openocd_path}\..\scripts\target\stm32f4x_stlink.cfg" -c init -c "reset init"
 - Under the "Debugger" tab find "GDBClient Setup/Executable" and point to the cross compiler (e.g. "C:\yotta\gcc\bin\arm-none-eabi-gdb.exe")

7. You should now be ready set breakpoints and run degugging in Eclipse

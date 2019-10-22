# SONOFF EXPLOITATION
A brief how to guide on why and how to hack a sonoff device.
### Introduction
In this post we will be discussing about how to load a custom firmware on the Sonoff device and control it using its Ip address.
### APPROACH
- We will be using the [`uart pins`](https://en.wikipedia.org/wiki/Universal_asynchronous_receiver-transmitter) on the device along with a [`PL2303`](https://www.instructables.com/id/USB-to-TTL-Converter-PL2303HX/) convertor to flash custom firmware(namely Tasmota firmware) on the device and then use a network mapping tool[`(nmap)`](https://en.wikipedia.org/wiki/Nmap) to get the ip address of the SONOFF basic.

### About The Device
- A sonoff device is basically a [ESP8266](https://en.wikipedia.org/wiki/ESP8266) wifi module which can be controlled using the mobile application and its function is to switch on/off the relay on the sonoff device hence it can be used to make any electronic appliance smart(adds control over wifi functionality).

## Hacking Sonoff – hardware
Before you open a device like this, remember to only program it using power from the `FTDI` programmer. `DO NOT USE MAINS` to power up the chip for programming.

Sonoff has exposed all the pins needed to flash the software. To make the job easier, we can add 5 header pins to the board. So, we can use jumper wires when hacking Sonoff devices.

![](1.jpg)

The pinout of the Sonoff basic is as follows:

Pay attention to the board orientation to connect the correct wires between your device and FTDI converter.

| Sonoff Basic  |   | FTDI          |
| ------------- |---| ------------- |
| 3.3V          | - | 3.3V          |
| TX            | - | RX            |
| RX            | - | TX            |
| GND           | - | GND           |

### Tools Required
- [**Esptool**](https://github.com/espressif/esptool)
  The esptool is used to flash and create backup of the original firmware.
- [**Nmap**](https://en.wikipedia.org/wiki/Nmap)
The nmap tool is used to scan for networks and ports across a network, your pc is connected to.This tool will be used to get the ip address of the SONOFF basic.
- [**pyserial**](https://pypi.org/project/pyserial/)


### Procedure
#### INSTALLING NMAP:-
- To install nmap tool you have type the below command on your terminal
```console
sudo apt-get install nmap
```
#### INSTALLING ESPTOOL:-
- To install esptool make sure you have the python3 installed on your computer,if you don’t have python3 installed,enter the below command on your terminal.
```console
sudo apt-get install python3
```
Now once you have the python3 installed on your 
Pc,go to the below link and download-source code (tar.gz ) file and extract the folder in the Documents directory.
https://github.com/espressif/esptool/releases
Then go to the command line and enter the below command-
```console
cd/documents/esptool
```

### FIRMWARE BACKUP AND FLASHING:
To create a backup of the firmware you will have to first check for the port your SONOFF is connected to,to do so type the following in the terminal:
```console
ls /dev/tty
```
tty/ACM(number) or ttyUSB(number) is the required port. Note it somewhere.
![]()
- Now in the esptool directory type the following command-
```console
sudo ./esptool.py --port /dev/ttyUSB(number) read_flash 0x00000 0x100000 image1M.bin
```
This should create a backup of the firmware by the name image1M.bin in the esptool directory.
- To flash the new firmware visit the website given below,scroll to the bottom and download sonoff.bin file and save it in the esptool folder that was described in the previous steps.
https://github.com/arendst/Sonoff-Tasmota/releases
- Now type the following in the command line:
```console
sudo ./esptool.py --port /dev/ttyUSB(number) write_flash -fs 1MB -fm dout 0x0 sonoff.bin
```
### CONNECTING TO ROUTER:
The device should be flashed with the custom firmware now,wait for 2-3 sec,the SONOFF will restart itself and create a hotspot by a similar name-- sonoff-4996
- Connect to the hotspot and you should be redirected to a page where you can save ssID and passwords of your router,click on save changes.
- After this step the device will restart itself and will automatically connect to your router.

### CONTROLLING THE DEVICE:
- Now to control the device you need to get the ip address of the device for which we will use nmap tool.
- Type the following in the command line:
```console
Ifconfig
```
![]()
**NOTE-make sure your pc and the SONOFF is connected to the same network.**
- After the scan is completed you will be able to see the ip address of the SONOFF device and also ip addresses of all the devices connected to that network.
- Enter the ip address in your web browser and you will be able to get a similar control menu like this.
![]()

### USE:
- With the help of this menu you can get access to the victim’s ssID and passwords and even deny his access to the device.
- Flashing this custom firmware increases the functionality of the device.
- Passwords and wiffi settings can be changed easily.

### Reference
[notenoughtech](https://notenoughtech.com/home-automation/esp/is-it-worth-hacking-sonoff-basic/)




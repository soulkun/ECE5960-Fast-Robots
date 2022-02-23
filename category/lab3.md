---
layout: category
title: Lab 3
---

# Sensors

## 0. Objective
The purpose of this lab is to soldering pins and tune ToF / IMU sensors - the faster the robot can sample and the more it can trust a sensor reading, the faster it is able to drive.

## 1. Soldering and connections setup
Consider the long term scalable and easy maintenance, I bought a **[SparkFun Qwiic MultiPort](https://www.sparkfun.com/products/18012)** for make connections between Artemis board to other sensors, and a **[SparkFun Qwiic Cable Kit](https://www.sparkfun.com/products/15081)** used for wiring to sensors.

Here are the two ToF sensors with right angle male pin header soldered on.
One will mount at the front of the vehicle, and the other one mount at the side (Vertical mounting).
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/3/1.jpg)

And here is the IMU with a right angle female socket header soldered on. In this case, the IMU can be placed vertically or horizontally and only need a thin space inside the vehicle.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/3/2.jpg)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/3/3.jpg)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/3/4.jpg)

On the Artemis Nano board side, I soldered a blue **[Amphenol 76382-307LF](https://www.digikey.com/en/products/detail/amphenol-icc-fci/76382-307LF/1001597)** male pin header on the side which have the Qwiic port, considered connections between ToF XSHUT pins and future motor controller transmission with the Artemis, totally I need `2 XSHUT + 4 motor control logic = 6 pins`. Also, a **[NKK AS13AP](https://www.digikey.com/en/products/detail/nkk-switches/AS13AP/1014820)** slide switch has been soldered on the PSWC pins, this makes less plugging-unplugging damage on the USB-C port when I debug sensors frequently. (I had an ESP8266 board with the micro-USB socket dead due to the frequent plugging and unplugging, solder pads on that board have been pulled-off and it's hard to fix it and solder a new socket connector...)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/3/5.jpg)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/3/6.jpg)

The whole thing looks like this.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/3/7.jpg)


Successfully started the jupyter lab on local address **`127.0.0.1`** on port **`8888`**.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/5.jpg)

However, when I try to go through the first code block in the the demo.ipynb, it gives me an error said **"Only Windows 10 is supported."**
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/6.jpg)

So second, I installed Windows 10 on the VmWare Workstation and go over again the environment setup part inside the virtual machine. This time the first code block passed. And I powered on Artemis Nano board, upload the ble_arduino code and successfully get the bluetooth MAC address. The MAC address is **`C0:07:E0:8D:9A:44`**. But the virtual machine cannot detect the Artemis Nano board, even I passed Intel bluetooth hardware to the VmWare.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/7.jpg)

At this point, I did multiple research and look up through the internet, using the external bluetooth adapter; also contacted on of the TA via Zoom meeting, but nothing help.
Finally, on the next day I install the Windows 10 natively on my laptop hard drive and re-build the virtual environment.
As shown below, it can detected the Artemis board and connect it.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/8.jpg)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/9.jpg)

## 2. 


## 3. 


## 4.

## 5.

## 6.
{% highlight c linenos %}

{% endhighlight %}

## 7. 
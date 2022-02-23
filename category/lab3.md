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

All in one.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/3/7.jpg)

## 2. Time of Flight Sensors
### Only one ToF
Based on the following connection setup.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/3/8.jpg)

Uploaded Example05_Wire_I2C, got ToF I2C address **`0x29`**.
This is what I expected, since the **[Pololu VL53L1X Description](https://www.pololu.com/product/3415)** states 
> "The sensor’s 7-bit slave address defaults to **`0101001b`** on power-up."

![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/3/9.jpg)

After testing three distance modes, I found out it really depends on the ambient light setting.
The short-distance mode works well under dark and bright light environments.
The medium and long-distance modes under the bright environment have incorrect readings when the testing distance is over 90cm, but they work well under the dark environments.
Based on this test, I would choose the short distance mode, considering ToF may face bright snow, bright sun, dark shadows.

### Two ToFs
Based on the following connection setup.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/3/10.jpg)

First, define XSHUT pins.
{% highlight c linenos %}
#define TOF1_SHUTDOWN_PIN 2
#define TOF2_SHUTDOWN_PIN 3
{% endhighlight %}

Second, create two ToF objects.
{% highlight c linenos %}
SFEVL53L1X TOF1;
SFEVL53L1X TOF2;
{% endhighlight %}

Third, in the setup part, set pin mode for both XSHUT pins, and set them to logical low.
{% highlight c linenos %}
pinMode(TOF1_SHUTDOWN_PIN, OUTPUT);
pinMode(TOF2_SHUTDOWN_PIN, OUTPUT);
digitalWrite(TOF1_SHUTDOWN_PIN, LOW);
digitalWrite(TOF2_SHUTDOWN_PIN, LOW);
{% endhighlight %}

Fouth, start up the first ToF sensor with 0x29 I2C address.
{% highlight c linenos %}
delay(50);
digitalWrite(TOF1_SHUTDOWN_PIN, HIGH);
delay(50);

if (TOF1.begin() != 0) //Begin returns 0 on a good init
    Serial.println("TOF1 failed to begin. Please check wiring.");
else
    TOF1.setI2CAddress(0x29);
Serial.println("TOF1 online!");
{% endhighlight %}

Fifth, start up the second ToF sensor with 0x30 I2C address.
{% highlight c linenos %}
delay(50);
digitalWrite(TOF2_SHUTDOWN_PIN, HIGH);
delay(50);

if (TOF2.begin() != 0) //Begin returns 0 on a good init
    Serial.println("TOF2 failed to begin. Please check wiring.");
else
    TOF2.setI2CAddress(0x30);
Serial.println("TOF2 online!");
{% endhighlight %}

Finally, start ranging on both ToF sensors, get distance data and print out both results in centimeters to serial.
{% highlight c linenos %}
void loop(void)
{
  TOF1.startRanging(); //Write configuration bytes to initiate measurement
  TOF2.startRanging(); //Write configuration bytes to initiate measurement
  while (!TOF1.checkForDataReady())
  {
    delay(1);
  }
  while (!TOF2.checkForDataReady())
  {
    delay(1);
  }
  int distance1 = TOF1.getDistance(); //Get the result of the measurement from the sensor
  int distance2 = TOF2.getDistance(); //Get the result of the measurement from the sensor
  TOF1.clearInterrupt();
  TOF2.clearInterrupt();
  TOF1.stopRanging();
  TOF2.stopRanging();

  Serial.print("Distance(mm): ");
  Serial.print(distance1);
  Serial.print(", ");
  Serial.print(distance2);

  Serial.println();
}
{% endhighlight %}
## 3. 


## 4.

## 5.

## 6.
{% highlight c linenos %}

{% endhighlight %}

## 7. 
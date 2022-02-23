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

Successfully read data from both ToF sensors.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/3/11.jpg)

### Distance Mode Experiment
**Note: This experiment collaborate with Tongqing Zhang (TZ422).**

We set a ruler on the ground, make the 450cm position as the zero point, use a tape to mount two sensors on the back of the laptop screen, one sensor tunes to the short mode and the other one tunes to long mode (setDistanceModeMedium() is not a callable function nor implement in the VL53L1X library according to **[HERE](https://github.com/sparkfun/SparkFun_VL53L1X_Arduino_Library/issues/40)**, thus we disregarded this and only test for short and long). The experiment operation is one person moves the box backward and the other person records the data.

![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/3/12.jpg)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/3/13.jpg)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/3/14.jpg)

To calibrate the laptop screen angle, make sure it is perpendicular to the ground, in order to avoid offsets when measuring long distances, we use the Bosch GLM 20 laser measure to enforce the actual distance.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/3/15.jpg)

Below are the data we captured. It seems the Short mode stop working when we go over 240 cm, but based on accuracy, the short mode always stands out. The long mode is inaccurate especially when measure longer distances.

| Physical Distance (cm) | Short Mode (Left ToF) (cm) | Long Mode (Right ToF) (cm) |
|------------------------|----------------------------|----------------------------|
|                     20 |                       19.0 | 19.0                       |
|                     40 |                       39.3 | 38.7                       |
|                     60 |                       59.4 | 58.6                       |
|                     80 |                       79.8 | 78.9                       |
|                    100 |                      100.0 | 98.7                       |
|                    120 |                      119.7 | 118.4                      |
|                    140 |                      139.6 | 137.9                      |
|                    160 |                      159.2 | 156.7                      |
|                    180 |                      178.1 | 176.0                      |
|                    200 |                      197.5 | 194.5                      |
|                    220 |                      216.4 | 212.0                      |
|                    240 |                        0.0 | 230.0                      |
|                    260 |                        0.0 | 247.1                      |
|                    280 |                        0.0 | 264.9                      |
|                    300 |                       32.9 | 270.0                      |
|                    320 |                       19.6 | 291.2                      |

Later I found this table from the **[VL53L1X Datasheet](https://www.pololu.com/file/0J1506/vl53l1x.pdf)** talking about ambient light parameter. We redo the test and found out there is no significant difference between dark and bright environments. Different color surface makes very tiny impact. But the sensor is sensitive to different materials, since they deflects light differently.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/3/16.jpg)

Based on this experiment, I would use the short distance mode since it gives me high accuracy.

## 3. Additional tasks
1. 
Ultrasonic distance sensor emits high-frequency sound waves towards the target object and receives the reflects back, using the time differences to calculate the distance. They are not affected by object colors or ambient lights, but the low resolution, slow refresh rate, and limited working distances would be a pain, and they are not suitable for fast-moving objects.
LiDAR measures the range of targets through light waves from a laser, instead sound waves. This type sensors have high accuracy no matter it's in extremely dark or bright environments, also has the higest refresh rate for fast-moving objects. But they are expensive cost and since it emitting laser, can damage human eyes.

2. 
Based on data sheet and the [Qwiic Distance Sensor Hookup Guide](https://learn.sparkfun.com/tutorials/qwiic-distance-sensor-vl53l1x-hookup-guide/all), the timing budget is the amount of time over which a measurement is taken. This can be set to any of the following: 
**`20, 33, 50, 100 (default), 200, 500`**. The 20ms is only available in short distance mode. On the other hand, the setIntermeasurementPeriod() allows to change the time alotted for a measurement, default is 100ms.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/3/17.jpg)

3. 
The goal is to get high sampling rate, avoid **`Signal Fail`** event, like overclocking CPU. After serval test with these two functions, I found out the below settings would be a stable one and gives me 25 Hz.
{% highlight c linenos %}
distanceSensor.setDistanceModeShort();
distanceSensor.setTimingBudgetInMs(20);
distanceSensor.setIntermeasurementPeriod(50);
{% endhighlight %}

The range status keeps "Good" while the distance changes rapidly.
To enlarge this screenshot, right click it and open in a separate window.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/3/18.jpg)


## 4. IMU
### Setup the IMU
First, here is the wiring setup.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/3/19.jpg)

Second, I flashed **`Example05_Wire_I2C.ino`** first to detect IMU address, and I got **`0x68`**.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/3/20.jpg)

Third, open the \Arduino\libraries\SparkFun_ICM-20948\SparkFun_ICM-20948_ArduinoLibrary-master\examples\Arduino\Example1_Basics. 
On the line #22, the **`AD0_VAL`** (address value at the zero bit) should be set to **`0`**, since the LSB of 0x68 is zero.
Also, there is a **`ADDR+1`**jumper on the back side of the IMU, the jumper is closed by default.
{% highlight c linenos %}
#define AD0_VAL 0
{% endhighlight %}

There is a reference frames sign on the IMU surface.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/3/21.jpg)

I discovered when I give a acceleration towards to one of the arrow direction, the acceleration on that axis reading is negative.
When I move the IMU suddenly to one direction, the gyroscope generates a pulse-liked curve. It will first goes positive(negative) side then negative(positive) side and finally goes back to zero.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/3/22.jpg)

### Accelerometer
1. 
{% highlight c linenos %}
float pitch = 180 * atan2(sensor->accX(), sensor->accZ()) / M_PI;
float roll = 180 * atan2(sensor->accY(), sensor->accZ()) / M_PI;

SERIAL_PORT.print("Pitch: ");
printFormattedFloat(pitch, 3, 2);
SERIAL_PORT.print("°    Roll: ");
printFormattedFloat(roll, 3, 2);
SERIAL_PORT.print("°");
SERIAL_PORT.println();
{% endhighlight %}


|       |  -90  |   0   |   +90  |
|-------|:-----:|:-----:|:------:|
| Pitch | 88.33 |  0.32 | -88.79 |
| Roll  | 89.25 | -0.24 | -88.50 |
### Gyroscope
## 5. Additional tasks

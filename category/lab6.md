---
layout: category
title: Lab 6
---

# Closed-loop control (PID)

## 0. Objective
The purpose of this lab is to get experience with PID control, discover which works best for the system (P, PI, PID, PD)

## 1. Prelab
In order to transmit my time v.s. duty cycle data via the BLE, I get a unique UUID **`D4FAA1B0-CA9E-457F-BBE3-537DC0B77EBB`** by using an online UUID gnerator, and added a new characteristics called **`PIDstring`** in my **ble_arduino.ino** code.

{% highlight c linenos %}
//////////// BLE UUIDs ////////////
#define BLE_UUID_PID_STRING "D4FAA1B0-CA9E-457F-BBE3-537DC0B77EBB"

//////////// Global Variables ////////////
BLECStringCharacteristic tx_characteristic_PIDstring(BLE_UUID_PID_STRING, BLERead | BLENotify, MAX_MSG_SIZE);

// Add BLE characteristics
testService.addCharacteristic(tx_characteristic_PIDstring);
{% endhighlight %}

Also, added a corresponding entry in **connection.yaml**.
{% highlight yaml linenos %}
PID_STRING: 'D4FAA1B0-CA9E-457F-BBE3-537DC0B77EBB'
{% endhighlight %}

## 2. Task A: Don’t Hit the Wall!!

### Switched to Pololu ToF library
To get a native support, I follow the lab instructions, switched my ToF library from Sparkfun to Pololu and replaced all statements by using the **[Pololu library link](https://github.com/pololu/vl53l0x-arduino)** that lab instructions provided. However, when I setup everything and ready to go, the serial monitor keeps telling me **Failed to detect and initialize sensor!**. After I checked our ToF chip, it is **`VL53L1X`**, not **VL53L0X**, so the correct library should be **[this](https://github.com/pololu/vl53l1x-arduino/)**. I go over the code library and did not find **`setProxIntegrationTime()`** function.

The code shows two ToF sensors initialization, set in long-distance mode with 50000us (50ms) timing budget and 50 ms inter-measurement period.

{% highlight c linenos %}
#include <VL53L1X.h>
// The number of sensors in your system.
const uint8_t sensorCount = 2;
// The Arduino pin connected to the XSHUT pin of each sensor.
const uint8_t xshutPins[sensorCount] = { 8, 7 };

VL53L1X sensors[sensorCount];

void setup()
{
    // Disable/reset all sensors by driving their XSHUT pins low.
    for (uint8_t i = 0; i < sensorCount; i++)
    {
        pinMode(xshutPins[i], OUTPUT);
        digitalWrite(xshutPins[i], LOW);
    }
    
    // Enable, initialize, and start each sensor, one by one.
    for (uint8_t i = 0; i < sensorCount; i++)
    {
        // Stop driving this sensor's XSHUT low. This should allow the carrier
        // board to pull it high. (We do NOT want to drive XSHUT high since it is
        // not level shifted.) Then wait a bit for the sensor to start up.
        pinMode(xshutPins[i], INPUT);
        delay(10);
    
        sensors[i].setTimeout(500);
        if (!sensors[i].init())
        {
            Serial.print("Failed to detect and initialize sensor ");
            Serial.println(i);
            while (1);
        }
    
        // Each sensor must have its address changed to a unique value other than
        // the default of 0x29 (except for the last one, which could be left at
        // the default). To make it simple, we'll just count up from 0x2A.
        sensors[i].setAddress(0x2A + i);
        sensors[i].setDistanceMode(VL53L1X::Long);
        sensors[i].setMeasurementTimingBudget(50000);
        sensors[i].startContinuous(50);
    }
}

void loop()
{
    get_tof();
}

void get_tof()
{
    sensors[0].read();
    if (sensors[0].timeoutOccurred()) { Serial.print(" Front ToF TIMEOUT"); }
    sensors[1].read();
    if (sensors[1].timeoutOccurred()) { Serial.print(" Side ToF TIMEOUT"); }
}
{% endhighlight %}

### PID library
There exist an **[Arduino PID library](https://github.com/br3ttb/Arduino-PID-Library/)** written by Brett Beauregard but has out of date, an enhanced version with greater accuracy than the legacy is called **[ArduPID](https://github.com/PowerBroker2/ArduPID)** by PowerBroker2.

For proportional, it is used to control the output in proportion to the error, it will react immediately to an error value and try bring the process value close to the set point. The higher the proportional gain, the faster the reaction of the controller.

Too Low: Controller reaction speed will be slow, and may not be stable.

Too High: Controller may overshoot and start oscillating.
{% highlight c linenos %}
kp = pIn;
curInput    = *input;
curSetpoint = *setpoint;
curError    = curSetpoint - curInput;
pOut = kp * curError;
{% endhighlight %}


For integral, it will continue to accumulate over time until the set point is reached. The longer it takes to reach the setpoint, the more the integral influences the output.

Too Low: Controller may neverreach the setpoint and may be slow.

Too High: Controller may overshoot and start oscillating.
{% highlight c linenos %}
ki = iIn * (timer.timeDiff / 1000.0);

lastError   = curError;
curInput    = *input;
curSetpoint = *setpoint;
curError    = curSetpoint - curInput;

iOut += (ki * ((curError + lastError) / 2.0)); // Trapezoidal integration
iOut = constrain(iOut, windupMin, windupMax);  // Prevent integral windup
{% endhighlight %}


For derivative, it looks at the ramp rate or how fast the process value is reaching the setpoint, and limits the output to prevent overshooting.

Too Low: Controller may react normally, but no means of limiting overshoot.

Too High: Controller may be unstable, and have undesired reaction to distortion on the actual value.

To prevent the derivatie kick issue, instead of adding (Kd * derivative of Error), I subtract (Kd * derivative of Input). This is known as using **“Derivative on Measurement”**.
{% highlight c linenos %}
kd = dIn / (timer.timeDiff / 1000.0);

lastInput     = curInput;
double dInput = *input - lastInput;

dOut = -kd * dInput; // Derrivative on measurement
{% endhighlight %}


Below code shows the combined implementation of the PID calculation.
{% highlight c linenos %}
kp = pIn;
ki = iIn * (timer.timeDiff / 1000.0);
kd = dIn / (timer.timeDiff / 1000.0);

if (direction == BACKWARD)
{
    kp *= -1;
    ki *= -1;
    kd *= -1;
}

lastInput    = curInput;
lastSetpoint = curSetpoint;
lastError    = curError;

curInput    = *input;
curSetpoint = *setpoint;
curError    = curSetpoint - curInput;

double dInput = *input - lastInput;

if (pOnType == P_ON_E)
    pOut = kp * curError;
else if (pOnType == P_ON_M)
    pOut = -kp * dInput;

iOut += (ki * ((curError + lastError) / 2.0)); // Trapezoidal integration
iOut = constrain(iOut, windupMin, windupMax);  // Prevent integral windup

dOut = -kd * dInput; // Derrivative on measurement

double newOutput = bias + pOut + iOut + dOut;
newOutput        = constrain(newOutput, outputMin, outputMax);
*output          = newOutput;
{% endhighlight %}
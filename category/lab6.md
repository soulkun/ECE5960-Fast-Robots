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
#define BLE_UUID_TEST_SERVICE "9A48ECBA-2E92-082F-C079-9E75AAE428B1"
#define BLE_UUID_RX_STRING "9750f60b-9c9c-4158-b620-02ec9521cd99"
#define BLE_UUID_TX_STRING "27616294-3063-4ecc-b60b-3470ddef2938"
#define BLE_UUID_TX_FLOAT "f235a225-6735-4d73-94cb-ee5dfce9ba83"
#define BLE_UUID_PID_STRING "D4FAA1B0-CA9E-457F-BBE3-537DC0B77EBB"

//////////// Global Variables ////////////
BLEService testService(BLE_UUID_TEST_SERVICE);

BLECStringCharacteristic rx_characteristic_string(BLE_UUID_RX_STRING, BLEWrite, MAX_MSG_SIZE);

BLEFloatCharacteristic tx_characteristic_float(BLE_UUID_TX_FLOAT, BLERead | BLENotify);
BLECStringCharacteristic tx_characteristic_string(BLE_UUID_TX_STRING, BLERead | BLENotify, MAX_MSG_SIZE);
BLECStringCharacteristic tx_characteristic_PIDstring(BLE_UUID_PID_STRING, BLERead | BLENotify, MAX_MSG_SIZE);

// Add BLE characteristics
testService.addCharacteristic(tx_characteristic_float);
testService.addCharacteristic(tx_characteristic_string);
testService.addCharacteristic(rx_characteristic_string);
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
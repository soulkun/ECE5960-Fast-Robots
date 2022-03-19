---
layout: category
title: Lab 6
---

# Closed-loop control (PID)

## 0. Objective
The purpose of this lab is to get experience with PID control.

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
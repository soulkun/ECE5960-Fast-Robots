---
layout: category
title: Lab 9
---

# Mapping

## 0. Objective
Build up a map of a static room by placing the robot in a couple of marked up locations, and have it spin around its axis while measuring the ToF readings.

## 1. Angular speed control
I choose to use the angular speed control, based on raw gyroscope values.
Calculate the yaw angle degrees based on the gyroscope Z axis, then make a left/right spin. After several tests, I achieved a maximum of 18 degrees per step(spin).
{% highlight c linenos %}
float yaw = 0, gyrZ = 0;

while(yaw < setpoint)
{
    get_tof();

    whilt(millis() - current_time < timelimit)
    {
        previous_time = current_time;
        current_time = millis();
        time_diff = current_time - previous;

        get_IMU();
        gyrZ += myICM.gyrZ() * time_diff / 1000.0;
        error = gyrZ - expected;

        // Perform PID calculations
        // ...
        // and turn slightly left/right
        spin();
        
        yaw += gyrZ;
    }
}

void spin()
{
    // Right turn
    if(PID_out > maxspeed)
    {
        analogWrite(A0, 0);
        analogWrite(A1, maxspeed);
        analogWrite(A2, 0);
        analogWrite(A3, maxspeed);
    }
    else if(PID_out >= 0)
    {
        analogWrite(A0, 0);
        analogWrite(A1, PID_out);
        analogWrite(A2, 0);
        analogWrite(A3, PID_out);
    }
    // Left turn
    else if(PID_out <= maxspeed)
    {
        analogWrite(A0, maxspeed);
        analogWrite(A1, 0);
        analogWrite(A2, maxspeed);
        analogWrite(A3, 0);
    }
    else
    {
        analogWrite(A0, -PID_out);
        analogWrite(A1, 0);
        analogWrite(A2, -PID_out);
        analogWrite(A3, 0);
    }
}
{% endhighlight %}

## 2. Read out Distances
Note: This part collaborates with Ruohan Liu (RL592).

I put the robot on the ground and use BLE to send out a start signal, make the robot turns at 18 degrees each time, record the ToF readings then send all data back.
Field test on **`(0, 3)`**. **[(Video Demo)](https://youtu.be/RK34eccuO08)**

Field test on **`(5, 3)`**. **[(Video Demo)](https://youtu.be/l_TlO161rFE)**

Field test on **`(5, -3)`**. **[(Video Demo)](https://youtu.be/FU1rzru13iM)**


below are the ToF reading data.

**`(0, 0)`**

| **Angle (degree)** | **ToF (mm)** |
|--------------------|:------------:|
|            0 (360) |      761     |
|                 18 |      839     |
|                 36 |     2110     |
|                 54 |     1689     |
|                 72 |     1428     |
|                 90 |     1339     |
|                108 |     1363     |
|                126 |     1235     |
|                144 |      723     |
|                162 |      601     |
|                180 |     1453     |
|                198 |     1511     |
|                216 |     1705     |
|                234 |     1821     |
|                252 |     1413     |
|                270 |     1163     |
|                288 |      822     |
|                306 |     1368     |
|                324 |     1738     |
|                342 |     2314     |


## 3. Merge and Plot your readings

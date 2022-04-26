---
layout: category
title: Lab 11
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


Below are the ToF reading data.

**`(0, 0)`**

| **Angle (degree)** | **ToF (mm)** |
|-------------------:|:------------:|
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

**`(0, 3)`**

| **Angle (degree)** | **ToF (mm)** |
|-------------------:|:------------:|
|            0 (360) |     1977     |
|                 18 |     1698     |
|                 36 |      635     |
|                 54 |      433     |
|                 72 |      367     |
|                 90 |      352     |
|                108 |      378     |
|                126 |      489     |
|                144 |      689     |
|                162 |      718     |
|                180 |      664     |
|                198 |      815     |
|                216 |     2503     |
|                234 |     2190     |
|                252 |     1948     |
|                270 |     2207     |
|                288 |     2542     |
|                306 |      909     |
|                324 |      803     |
|                342 |     1884     |

**`(5, 3)`**

| **Angle (degree)** | **ToF (mm)** |
|-------------------:|:------------:|
|            0 (360) |      392     |
|                 18 |      462     |
|                 36 |      560     |
|                 54 |      418     |
|                 72 |      389     |
|                 90 |      431     |
|                108 |      529     |
|                126 |      732     |
|                144 |     1470     |
|                162 |     2178     |
|                180 |     2457     |
|                198 |      571     |
|                216 |      423     |
|                234 |      369     |
|                252 |     2191     |
|                270 |     1227     |
|                288 |      738     |
|                306 |      532     |
|                324 |      459     |
|                342 |      433     |

**`(-3, -2)`**

| **Angle (degree)** | **ToF (mm)** |
|-------------------:|:------------:|
|            0 (360) |     3007     |
|                 18 |     1796     |
|                 36 |     1791     |
|                 54 |     2802     |
|                 72 |     2213     |
|                 90 |      684     |
|                108 |      652     |
|                126 |      701     |
|                144 |      853     |
|                162 |      776     |
|                180 |      660     |
|                198 |      610     |
|                216 |      655     |
|                234 |      816     |
|                252 |      870     |
|                270 |      724     |
|                288 |      682     |
|                306 |      710     |
|                324 |      881     |
|                342 |      823     |

**`(5, -3)`**

| **Angle (degree)** | **ToF (mm)** |
|-------------------:|:------------:|
|            0 (360) |      396     |
|                 18 |      419     |
|                 36 |      490     |
|                 54 |      628     |
|                 72 |     1037     |
|                 90 |     2276     |
|                108 |      706     |
|                126 |      768     |
|                144 |     1638     |
|                162 |     2891     |
|                180 |     3022     |
|                198 |     1325     |
|                216 |      744     |
|                234 |      531     |
|                252 |      452     |
|                270 |      428     |
|                288 |      465     |
|                306 |      575     |
|                324 |      457     |
|                342 |      376     |

## 3. Merge and Plot your readings
Below graph generated by **`matplotlib.pyplot`**
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/9/plot.png)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/9/line.png)
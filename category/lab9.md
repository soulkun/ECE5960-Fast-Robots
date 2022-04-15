---
layout: category
title: Lab 9
---

# Mapping

## 0. Objective
Build up a map of a static room by placing the robot in a couple of marked up locations, and have it spin around its axis while measuring the ToF readings.

## 1. Angular speed control
I choose to use the angular speed control, based on raw gyroscope values.
Calculate the yaw angle degrees based on gyroscope Z axis, then make a left/right spin.
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


## 3. Merge and Plot your readings

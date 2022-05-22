---
layout: category
title: Lab 13
---

# Path Planning and Execution

## Objective
Have the robot navigate through a set of waypoints in the given environment as quickly and accurately as possible.

## Solution
Initially, I plan to use localization to finish this task. But after some tests, I found it is high coupling. If one of the beliefs is wrong, the rest of the waypoints would have a heavy drift and be unable to reach the final endpoint. Therefore, I switch to feedback control using the PID on ToF sensors from lab 6 to control forward/backward, and using the PID on IMU to control angular speed from lab 9 to make the robot turn in place. Also for turning, I discovered it is very hard to let the robot turn exactly 45 degrees or other degrees except for right angle, due to the frictions between the wheel and the ground, the ideal and the actual won't match perfectly. So, to solve this issue, I choose to turn the right angle to reach every waypoint, in this case, I added some temporary stops just to turn 90 degrees, and my route is shown below in cyan color.

PID on ToF sensors
{% highlight c linenos %}
#include "ArduPID.h"

ArduPID myController;
double input;
double output;
double setpoint = 300;
double p = 0.01;
double i = 0;
double d = 0;

void setup()
{
    myController.begin(&input, &output, &setpoint, p, i, d);
    myController.reverse();                // Uncomment if controller output is "reversed"
    myController.setOutputLimits(-220, 220);
    myController.setBias(0);               // Bias
    myController.setWindUpLimits(-10, 10); // Groth bounds for the integral term to prevent integral wind-up
    myController.start();
}

void loop()
{
    get_tof();
}

void get_tof()
{
    sensors[0].read();
    if (sensors[0].timeoutOccurred()) { Serial.print(" Front ToF TIMEOUT"); }

    int distance = sensors[0].ranging_data.range_mm;

    input = distance;         // Send data to PID input
    myController.compute();   // Calculate PID
    
    // Deadband settings
    if(output > 0)
      output += 35;
    else if(output < 0)
      output -= 35;

    Serial.print(distance);
    Serial.print(", ");
    Serial.print(output);
    Serial.print("\n");
}
{% endhighlight %}

Angular speed control
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

![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/13/route.png)

Second, I put the robot at each stop point and target waypoints, and measure the distance to the wall using the front ToF sensor. So that I can change setpoints according to each location and pass this value to the PID controller to make the robot move forward/backward. Below figure shows which point I measured and the distance in millimeters.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/13/1.png)

The robot will access the following points one by one in series.
{% highlight c linenos %}
(-4, -3)    <-- start
(-2, -3)    <-- temporary stop
(-2, -1)    <-- 1st waypoint
(1, -1)     <-- 2nd waypoint
(1, -3)     <-- temporary stop
(2, -3)     <-- 3rd waypoint
(5, -3)     <-- 4th waypoint
(5, -2)     <-- 5th waypoint
(5, 3)      <-- 6th waypoint
(0, 3)      <-- 7th waypoint
(0, 0)      <-- end
{% endhighlight %}

**[(Click here for Video Demo)](https://youtu.be/vXTa4QqsIEo)**

## Discussion
The result is acceptable, when the robot goes from the **`(5, -3)`** to **`(5, 3)`**, there is a little bit of drift that caused the rest of the waypoints not to too accurate, finally, the robot stops at the left side of the end point. An improvement could be make the robot stop multiple time between **`(5, -3)`** and **`(5, 3)`** to get more readings on the ToF sensor, and calculate the average value.
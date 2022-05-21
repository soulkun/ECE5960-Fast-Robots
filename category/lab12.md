---
layout: category
title: Lab 12
---

# Localization on the real robot

## Objective
The purpose of this lab is to perform localization, using only the update step of the Bayes filter on the actual robot.

### Test Localization in Simulation
Green: Ground Truth

Blue: Belief

Red: Odometry

As the screenshot shows below, the belief is almost same as the ground truth, therefore the bayes filter works very well.

**[(Click here for Video Demo)](https://youtu.be/tjYPgugYJic)**

![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/12/1.jpg)

### Observation Loop
In order to retrieve surrounding data, I need the robot turn 360 degrees and collecting ToF readings every 20 degrees. Recall the Lab 9, I use the angular speed control, based on raw gyroscope values. Calculate the yaw angle degrees based on the gyroscope Z axis, then make a left/right spin. This time, instead of just turning 20 degrees mechanically, I added a error traking features. For example, the first time it should turn exactly **`20`** degrees, but actually it turns 19 degrees, then the next time it would turn `**20+1**` degrees to correct the previous error, and so on.

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

### Localization

#### (-3, -2)
**[(-3, -2)(Click here for Video Demo)](https://youtu.be/Lq4bROWN0xE)**
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/12/Result_-3_-2.png)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/12/(-3_-2).png)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/12/(-3_-2)_noB.png)

#### (0, 3)
**[(0, 3)(Click here for Video Demo)](https://youtu.be/zE1E5iyn9uk)**
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/12/Result_0_3.png)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/12/(0_3).png)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/12/(0_3)_noB.png)

#### (5, -3)
**[(5, -3)(Click here for Video Demo)](https://youtu.be/YAzNEA4-UOQ)**
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/12/Result_5_-3.png)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/12/(5_-3).png)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/12/(5_-3)_noB.png)

#### (5, 3)
**[(5, 3)(Click here for Video Demo)](https://youtu.be/SFhDOW3IvvI)**
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/12/Result_5_3.png)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/12/(5_3).png)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/12/(5_3)_noB.png)
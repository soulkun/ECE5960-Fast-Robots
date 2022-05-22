---
layout: category
title: Lab 13
---

# Path Planning and Execution

## Objective
Have the robot navigate through a set of waypoints in the given environment as quickly and accurately as possible.

## Solution
Initially, I plan to use localization to finish this task. But after some tests, I found it is high coupling. If one of the beliefs is wrong, the rest of the waypoints would have a heavy drift and be unable to reach the final endpoint. Therefore, I switch to feedback control using the PID on ToF sensors from lab 6 to control forward/backward, and using the PID on IMU to control angular speed from lab 9 to make the robot turn in place. Also for turning, I discovered it is very hard to let the robot turn exactly 45 degrees or other degrees except for right angle, due to the frictions between the wheel and the ground, the ideal and the actual won't match perfectly. So, to solve this issue, I choose to turn the right angle to reach every waypoint, in this case, I added some temporary stops just to turn 90 degrees, and my route is shown below in cyan color.

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
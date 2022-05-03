---
layout: category
title: Lab 10
---

# Simulator

## 0. Objective
Setup and use the simulation environment. Control a virtual robot and use the live plotting tool.

## 1. Simulator & Plotter
### Simulator
The simulator was built by using the PyGame, it simulates a robot in the center and a map skeleton around it, which is the same map as we did in Lab 9.

In order to control the simulator and make the robot move, we can use the four arrow keys on the keyboard. Up/Down controls the linear velocity, and Left/Right controls the angular velocity. To freeze the robot, simply press the space bar. Press down the 'h' key will show a keyboard control legend on the left top.

### Plotter
On the other hand, the plotter graphs the odometry and the ground truth in real-time. There is an 'A' icon at the bottom left, which is an auto-fit feature to let the graph fit the current window.


To start the simulator and the plotter, execute the following command, then it shows four buttons, click on them and the corresponding window will show up.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/10/1.jpg)

Or use the start/stop/reset command blocks.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/10/2.jpg)


If anything screwed up, first stop both simulator and the plotter, then reset them and start.

## 2. Open Loop Control
The goal is to make the robot follows a set of velocity commands to generate a "square" shape loop.

To accomplish this, I use the **`set_vel(linear_vel, angular_vel)`** command to let the robot turn left 90 degrees for 1 second, then go forward at 0.5 m/s for 1 second, and repeat...

**[(Video Demo)](https://youtu.be/v4pSWZYt0R4)**

{% highlight python linenos %}
while cmdr.sim_is_running() and cmdr.plotter_is_running():
    pose, gt_pose = cmdr.get_pose()
    
    // Turn 90 degrees
    cmdr.set_vel(0, 90 * math.pi / 180.0)
    await asyncio.sleep(1)
    
    // Go forward at 0.5 m/s
    cmdr.set_vel(0.5, 0)
    await asyncio.sleep(1)

    
    cmdr.plot_odom(pose[0], pose[1])
    cmdr.plot_gt(gt_pose[0], gt_pose[1])
{% endhighlight %}

In the video demo, there is no way to draw a perfect square every time, this is because of using the **`await asyncio.sleep(1)`**, depending on the CPU and the OS, different machines could have different processing times and delays, there is no guarantee it's exactly 1 second.

I tried turn 89, 88, 87 or 87.5 degrees, sometimes work, sometimes not, so I go back to 90 degrees.

The green line shows the ground truth and the red line shows the odometry. Ideally they should be identical. But here in the simulator, due to the noise of the sensor, the odometry is not accurate.

![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/10/3.jpg)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/10/4.jpg)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/10/5.jpg)
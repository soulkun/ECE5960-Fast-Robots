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

The green line shows the ground truth, which is the actual position; and the red line shows the odometry, which is the sensor measurement data. Ideally they should be identical. But here in the simulator, due to the noise of the sensor, the odometry is not accurate.

**Comparison of odometry and ground truth. Odometry is not accurate.**
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/10/3.jpg)
**Cannot always execute the exact same shape**
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/10/4.jpg)
**After a couple of minutes, **
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/10/5.jpg)


## 3. Closed Loop Control

### Design a simple controller in your Jupyter notebook to perform a closed-loop obstacle avoidance.
To perform a closed-loop obstacle avoidance, I check the ToF sensor reading every time executing the while loop, if the distance is less than 0.5 meters, make a random turn between 1 rad/s and 3.14 rad/s until the distance is greater than 0.5 meters. After that, go forward at 0.5 m/s for 1 second. This is the safe settings.

{% highlight python linenos %}
# Task 2
import random
cmdr.reset_plotter()
cmdr.reset_sim()

# Loop for pose
while cmdr.sim_is_running() and cmdr.plotter_is_running():
    pose, gt_pose = cmdr.get_pose()
    
    // If ToF reading is less than half meter, pick a turn between 0.1/s rad and 3.14 rad/s
    if(cmdr.get_sensor() < 0.5):
        cmdr.set_vel(0, random.uniform(1, 3.14))
        await asyncio.sleep(1)

    // Then go straight at 0.5 m/s
    else:
        cmdr.set_vel(0.5, 0)
        await asyncio.sleep(1)

    
    cmdr.plot_odom(pose[0], pose[1])
    cmdr.plot_gt(gt_pose[0], gt_pose[1])
{% endhighlight %}

**`setpoint = 0.5, linear velocity = 0.5 m/s, angular velocity = [1, 3.14]`** **[(Video Demo)](https://youtu.be/BQ-BUjWXqn8)**


### By how much should the virtual robot turn when it is close to an obstacle?
There is no IMU equipped on the virtual robot, therefore I manually set angular velocity from 1 rad/s to 3.14 rad/s, use **`await asyncio.sleep(1)`** to turn for 1 second, as the video shown above, it works. After a couple tests, I found out the setpoint could be 0.3 meters (previous is 0.5 meters from the wall).

{% highlight python linenos %}
# Task 2
import random
cmdr.reset_plotter()
cmdr.reset_sim()

# Loop for pose
while cmdr.sim_is_running() and cmdr.plotter_is_running():
    
    
    if(cmdr.get_sensor() < 0.3):
        cmdr.set_vel(0, random.uniform(1, 3.14))
        await asyncio.sleep(1)
    else:
        cmdr.set_vel(1, 0)
        pose, gt_pose = cmdr.get_pose()
        cmdr.plot_odom(pose[0], pose[1])
        cmdr.plot_gt(gt_pose[0], gt_pose[1])
{% endhighlight %}

**`setpoint = 0.3, linear velocity = 1.0 m/s, angular velocity = [1, 3.14]`** **[(Video Demo)](https://youtu.be/VrqTkqzJ4w0)**

### At what linear speed should the virtual robot move to minimize/prevent collisions? Can you make it go faster?
Initially, I start from 0.5 m/s speed with setpoint = 0.5 m, it runs pretty smooth without hitting the wall. Then I lower the setpoint = 0.3 m, and raise the linear velocity to 1.0 m/s, for most of the time still works, only when the robot gets stuck in the corner. Finally, I reached linear velocity to 3.0 m/s for the fastest I can touch, also with setpoint = 0.4 for safety.
**`setpoint = 0.4, linear velocity = 3.0 m/s, angular velocity = [1, 3.14]`** **[(Video Demo)](https://youtu.be/7DCEvugKU_g)**

### How close can the virtual robot get to an obstacle without colliding?
If the linear velocity = 0.5 m/s, then the setpoint could be 0.28 m to avoid collision.
**`setpoint = 0.28, linear velocity = 0.5 m/s`** **[(Video Demo)](https://youtu.be/vyly6_8itTM)**

If the linear velocity = 1.0 m/s, then the setpoint could be 0.3 m to avoid collision.
**`setpoint = 0.3, linear velocity = 1 m/s`** **[(Video Demo)](https://youtu.be/QZ0e2s2r4NM)**

Or the linear velocity = 3.0 m/s, then the setpoint could be 0.3 m to avoid **[(Video Demo)](https://youtu.be/7DCEvugKU_g)**

### Does your obstacle avoidance code always work? If not, what can you do to minimize crashes or (may be) prevent them completely?
No, not always work. When the robot goes to a corner, or the robot ToF sensor points to a far target, or when the robot faces the wall at an angle, the edge of the robot will hit the object on the map or the wall. See the following picture.

![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/10/6.jpg)

One method to prevent this is to have another ToF setting on the side or set one at the top-left and the other at the top-right, therefore the robot has a width for scanning.
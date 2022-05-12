---
layout: category
title: Lab 11
---

# Grid Localization using Bayes Filter

## 0. Objective
The purpose of this lab is to implement grid localization using Bayes filter.

### Prelab
Downloaded the lab11 notebook and copied it into the notebooks directory as shown below.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/11/1.jpg)

### compute_control
This function basically computes the translation from A to B, both before and after the action, every translation takes three arguments, x and y coordinates, and orientation info. Below is the theory in graph.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/11/2.jpg)

My implementation is based on the following formulas, using the python math library.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/11/3.jpg)
{% highlight python linenos %}
def compute_control(cur_pose, prev_pose):
    delta_rot_1 = math.degrees(math.atan2(curr_pose[1] - prev_pose[1]), (curr_pose[0] - prev_pose[0])) - prev_pose[2]
    delta_trans = math.hypot((curr_pose[0] - prev_pose[0]), (curr_pose[1] - prev_pose[1]))
    delta_rot_2 = cur_pose[2] - prev_pose[2] - delta_rot_1
    
    return delta_rot_1, delta_trans, delta_rot_2
{% endhighlight %}

### odom_motion_model
### prediction_step
### sensor_model
### update_step

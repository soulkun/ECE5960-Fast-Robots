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
    delta_rot_1 = math.degrees(math.atan2((cur_pose[1] - prev_pose[1]), (cur_pose[0] - prev_pose[0]))) - prev_pose[2]
    delta_trans = math.hypot((cur_pose[0] - prev_pose[0]), (cur_pose[1] - prev_pose[1]))
    delta_rot_2 = cur_pose[2] - prev_pose[2] - delta_rot_1
    
    return delta_rot_1, delta_trans, delta_rot_2
{% endhighlight %}

### odom_motion_model
This function is to calculate the probability between the current position and the previous position. There are three parameters, cur_pose, prev_pose and u. The goal is to calculate a u_bar from set of cur_pose and prev_pose. The reason of why need to normalize angle is because the state space for orientation is from -180 deg to +180 deg.

The implementation is based on formulas below.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/11/4.jpg)

{% highlight python linenos %}
def odom_motion_model(cur_pose, prev_pose, u):
    p_u = compute_control(cur_pose, prev_pose)   
    p_rot1 = loc.gaussian(mapper.normalize_angle(p_u[0]-u[0]), 0, loc.odom_rot_sigma)
    p_trans = loc.gaussian(mapper.normalize_angle(p_u[1]-u[1]), 0, loc.odom_trans_sigma)
    p_rot2 = loc.gaussian(mapper.normalize_angle(p_u[2]-u[2]), 0, loc.odom_rot_sigma)

    prob = p_rot1 * p_trans * p_rot2
    
    return prob
{% endhighlight %}

### prediction_step
{% highlight python linenos %}
def prediction_step(cur_odom, prev_odom):
    u = compute_control(cur_odom, prev_odom)
    loc.bel_bar = np.zeros((mapper.MAX_CELLS_X, mapper.MAX_CELLS_Y, mapper.MAX_CELLS_A))
    
    for prev_x in range(0, mapper.MAX_CELLS_X):
        for prev_y in range(0, mapper.MAX_CELLS_Y):
            for prev_a in range(0, mapper.MAX_CELLS_A):
                if (loc.bel[prev_x, prev_y, prev_a] > 0.0001):
                    for curr_x in range(0, mapper.MAX_CELLS_X):
                        for curr_y in range(0, mapper.MAX_CELLS_Y):
                            for curr_a in range(0, mapper.MAX_CELLS_A):
                                prev_pose = mapper.from_map(prev_x, prev_y, prev_a)
                                curr_pose = mapper.from_map(curr_x, curr_y, curr_a)
                                loc.bel_bar[curr_x, curr_y, curr_a] += (odom_motion_model(curr_pose, prev_pose, u) * loc.bel[prev_x, prev_y, prev_a])
                                
    loc.bel_bar /= np.sum(loc.bel_bar)
{% endhighlight %}

### sensor_model
{% highlight python linenos %}
def sensor_model(obs):
    prob = []
    
    for i in range(0, mapper.OBS_PER_CELL):
        prob.append(loc.gaussian(loc.obs_range_data[i], obs[i], loc.sensor_sigma))

    return prob
{% endhighlight %}

### update_step
{% highlight python linenos %}
def update_step():
    for curr_x in range(mapper.MAX_CELLS_X):
        for curr_y in range(mapper.MAX_CELLS_Y):
            for curr_a in range(mapper.MAX_CELLS_A):
                loc.bel[curr_x, curr_y, curr_a] = np.prod(sensor_model(mapper.get_views(curr_x, curr_y, curr_a))) * loc.bel_bar[curr_x, curr_y, curr_a]
                
    loc.bel /= np.sum(loc.bel)
{% endhighlight %}


**[Without bel_bar (Video Demo)](https://youtu.be/yQHBSPJTcg4)**
**[With bel_bar (Video Demo)](https://youtu.be/hJg7BnJqIV4)**

### Credits
Collaborate with Tongqing Zhang (TZ422) and Lu Vincent (YL929).
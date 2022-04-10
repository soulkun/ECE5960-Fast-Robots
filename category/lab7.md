---
layout: category
title: Lab 7
---

# Kalman Filter

## 0. Objective
Implement a Kalman Filter, which will helps execute the behavior in Lab 6 fast.

## Task A
## 1. Kalman Filter Setup
Let **`sigma_1 = 100, sigma_2 = 100, sigma_3 = 20`**, then the process noise and the sensor noise covariance matrices are
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/7/1.jpg)

Since I am doing Task A, the C matrix would be **`C = np.array([[-1,0]])`**.

{% highlight c linenos %}
sigma_1 = 100;
sigma_2 = 100;
sigma_3 = 20;

sig_u=np.array([[sigma_1**2,0],[0,sigma_2**2]]) //We assume uncorrelated noise, therefore a diagonal matrix works.
sig_z=np.array([[sigma_4**2]])
{% endhighlight %}

Under constructions...
**[Video Demo](https://youtu.be/flHN8qgoR-I)**
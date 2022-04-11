---
layout: category
title: Lab 7
---

# Kalman Filter

## 0. Objective
Implement a Kalman Filter, which will helps execute the behavior in Lab 6 fast.

## Task A
## 1. Step Response
I set the duty cycle to **`90`**, then put the robot at the 1.6m distance from the wall.

![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/7/0.jpg)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/7/1.jpg)

The max speed is around **`1.7 m/s`**, therefore
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/7/2.jpg)


## 2. Kalman Filter Setup
Let **`sigma_1 = 10, sigma_2 = 10, sigma_3 = 20`**, then the process noise and the sensor noise covariance matrices are
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/7/3.jpg)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/7/4.jpg)
Since I am doing Task A, the C matrix would be **`C = np.array([[-1,0]])`**.

{% highlight c linenos %}
sigma_1 = 10;
sigma_2 = 10;
sigma_3 = 20;

sig_u=np.array([[sigma_1**2,0],[0,sigma_2**2]]) //We assume uncorrelated noise, therefore a diagonal matrix works.
sig_z=np.array([[sigma_3**2]])
{% endhighlight %}

## 3. Sanity Check Your Kalman Filter
{% highlight python linenos %}
A = np.array([[0,1], [0,-d/m]])
B = np.array([[0], [1/m]])
C = np.array([[-1,0]])
sig_u = np.array([[sigma_1**2,0], [0,sigma_2**2]])
sig_z = np.array([[sigma_3**2]])
mu = np.array([[2532], [0]])

def kf(mu,sigma,u,y):
    Ad = np.eye(2) + dt * A
    Bd = dt * B

    mu_p = Ad.dot(mu) + Bd.dot(u) 
    sigma_p = Ad.dot(sigma.dot(Ad.transpose())) + Sigma_u
    sigma_m = C.dot(sigma_p.dot(C.transpose())) + Sigma_z
    kkf_gain = sigma_p.dot(C.transpose().dot(np.linalg.inv(sigma_m)))

    y_m = y-C.dot(mu_p)
    mu = mu_p + kkf_gain.dot(y_m)    
    sigma = (np.eye(2)-kkf_gain.dot(C)).dot(sigma_p)

    return mu,sigma
{% endhighlight %}
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/7/5.png)

## 4. Implement the Kalman Filter on the Robot
{% highlight c linenos %}
#include <BasicLinearAlgebra.h>
using namespace BLA;

int sigma_3 = 20;
float d = 90.0/1700;
float m = 0.000282291;

Matrix<2,2> A = { 0, 1,
                  0, -d/m};

Matrix<2,1> B = { 0,
                 1/m };

Matrix<1,2> C = { -1, 0 };

Matrix<2,2> sig_u = {sigma_1*sigma_1, 0,
                     0, sigma_2*sigma_2};

Matrix<1> sig_z = {sigma_3 * sigma_3};

Matrix<2,1> mu = {0, 0};


t_0 = millis();
pre = millis();
get_tof();
mu = {(float)dis,0};
sigma = {0,0,
            0,0};
pre_err = mu(0,0) - (float)set_point;
i = 0;
integ = 0.0;
delay(10);
while(millis() - t_0 < timelimit){
    now = millis();
    d_t = now - pre;
    Delta_t = d_t/1000.0;
    u = {-(float)dc};
    
    //prediction:
    sigma_1 = 10 * 10 / Delta_t;
    sigma_2 = 10 * 10 / Delta_t;
    sig_u = {sigma_1, 0,
            0, sigma_2};
    A_d = {1, 1,
            0, 1-kf_d/kf_m*Delta_t};
    B_d = {0,
            Delta_t/kf_m};
    mu_p = A_d * mu + B_d * u;
    sigma_p = A_d * sigma * ~A_d + sig_u;
            
    get_tof();

    //update:
    KF_cur = C * sigma_p * ~C + sig_z;
    KF_curr = 1/KF_cur(0,0);
    KF_cur = {KF_curr};
    KF_gain = sigma_p * ~C * KF_cur;
    y = {(float)dis};
    y_m = y - C * mu_p;
    mu = mu_p + KF_gain * y_m;
    sigma = (I_2 - KF_gain * C) * sigma_p;
{% endhighlight %}

Without the Kalman Filter
**[Video Demo](https://youtube.com/shorts/9iHhhdpI9-c)**

With the Kalman Filter
**[Video Demo](https://youtu.be/Eszzcpb_Q5k)**
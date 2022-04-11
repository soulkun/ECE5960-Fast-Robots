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
    sig_p = Ad.dot(sigma.dot(Ad.transpose())) + Sigma_u
    sigma_m = C.dot(sig_p.dot(C.transpose())) + Sigma_z
    kkf_gain = sig_p.dot(C.transpose().dot(np.linalg.inv(sigma_m)))

    y_m = y-C.dot(mu_p)
    mu = mu_p + kkf_gain.dot(y_m)    
    sigma = (np.eye(2)-kkf_gain.dot(C)).dot(sig_p)

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

Matrix<2,2> sig_u = {sigma_1*sigma_1,   0,
                     0,                 sigma_2*sigma_2};

Matrix<1> sig_z = {sigma_3 * sigma_3};

Matrix<2,1> mu = {0, 0};


previous_time = millis();
get_tof();
mu = {ToF_reading, 0};
sig = {0, 0,
       0, 0};
previous_error = mu(0, 0) - setpoint;

while(millis() - previous_time < timelimit)
{
    current_time = millis();
    d_t = current_time - previous_time;
    A_d = {1, 1,
           0, 1-d/m*dt};
    B_d = {0,
          dt/m};
    mu_p = A_d * mu + B_d * 90;
    sig_p = A_d * sig * ~A_d + sig_u;
    sig_m = C * sig_p * ~C + sig_z;
    sig_m = {1/sig_m(0,0);};
    inv_sig_m = sig_m;
    kf_gain = sig_p * ~C * inv_sig_m;
    y = {dis};
    y_m = y - C * mu_p;
    mu = mu_p + KF_gain * y_m;
    sig = (identMtx - kf_gain * C) * sig_p;
}
{% endhighlight %}

Test using **`P = 0.5, I = 0, D = 18`**.
Without the Kalman Filter
**[Video Demo](https://youtube.com/shorts/9iHhhdpI9-c)**

With the Kalman Filter
**[Video Demo](https://youtu.be/Eszzcpb_Q5k)**

Compare two outcomes, the one with the Kalman filter has the smoothest motion.
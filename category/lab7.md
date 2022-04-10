---
layout: category
title: Lab 7
---

# Kalman Filter

## 0. Objective
Implement a Kalman Filter, which will helps execute the behavior in Lab 6 fast.

## Task A
## 1. Kalman Filter Setup
Let **`sigma_1 = 10, sigma_2 = 10, sigma_3 = 20`**, then the process noise and the sensor noise covariance matrices are
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/7/1.jpg)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/7/2.jpg)
Since I am doing Task A, the C matrix would be **`C = np.array([[-1,0]])`**.

{% highlight c linenos %}
sigma_1 = 10;
sigma_2 = 10;
sigma_3 = 20;

sig_u=np.array([[sigma_1**2,0],[0,sigma_2**2]]) //We assume uncorrelated noise, therefore a diagonal matrix works.
sig_z=np.array([[sigma_3**2]])
{% endhighlight %}

## 2. Sanity Check Your Kalman Filter
{% highlight python linenos %}
A = np.array([[0,1],[0,-d/m]])
B = np.array([[0],[1/m]])
C = np.array([[-1,0]])
sig_u = np.array([[sigma_1**2,0],[0,sigma_2**2]])
sig_z = np.array([[sigma_3**2]])
mu = np.array([[2532],[0]])

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
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/7/3.png)
## 3. Implement the Kalman Filter on the Robot

Under constructions...
**[Video Demo](https://youtu.be/flHN8qgoR-I)**
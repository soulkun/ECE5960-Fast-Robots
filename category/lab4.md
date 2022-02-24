---
layout: category
title: Lab 4
---

# Characterize your car

## 1. (A) What are the dimensions of the car?
18.0cm long
14.7cm width
8.0cm height

This is easy, I use my tape measure to finish this.

## 2. (A) What is the weight?
519 grams

Compare to my laptop, which is 2 kg, the car is really light, to measure the car, first I put my laptop on the scale, and then put the car onto my laptop, calculate the weight differences.

## 3. (A) How long does it take to charge?
Before doing this experiment, I deeply discharged one of the battery by playing with the car first, then by just power on the car and left it with the LED flashing. I use the USB charging cable that comes with this car, plug into my USB Power Meter then plug them into my PC USB port.
The meter shows when the battery is in the charging status, 4.88V, 0.25A, 1.2W. Then I setup my GoPro camera to record the charging progress, it takes 2h 11mins to fully charged.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/4/1.jpg)

## 4. (A) What is the battery life time?
This is a LiPo battery with model number "HPV 852540", 650mAh capacity and output 3.7 Volts, I google it try to find any data sheet that states life cycle, unfortunately I found nothing. Based on typical LiPo battery, it should have 300 life cycles. Therefore, I use the stopwatch on my phone to assist me, and keep playing the car until the battery has been dry out. Record shows about 7 mins play time.

## 5. (B) What stunts can it do? Be sure to note the sequence of commands that corresponds with each stunt.
### Note: This experiment collaborate with Tongqing Zhang (TZ422) and Vincent Lu (YL929).
We found one stunt that the car can dancing itself.
First, hold the left/right key to let the car spinning.
Second, release the left/right key and at the sametime, hold the up/down key, the car will start dancing.
Click **[here](https://www.youtube.com/watch?v=s1zcrImvJYE)** if the video does not show.
<div class="video-container">
  <iframe width="640" height="360" src="https://www.youtube.com/watch?v=s1zcrImvJYE" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>


## 6. (B) How reliably does the robot turn around its own axis?
The left/right spinning extremely stable.
Click **[here](https://youtu.be/pYaPH612VEU)** if the video does not show.
<div class="video-container">
  <iframe width="640" height="360" src="https://youtu.be/pYaPH612VEU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>
It's hard to keep a straight line forward; however, use the "down arrow" on the remote control to let the car go reversely, it can keep onto a straight line.

## 7. (B) What is the range of speed? (remember, this is battery dependent). Consider using the TOF sensors to help you measure this.
### Note: This experiment collaborate with Tongqing Zhang (TZ422) and Vincent Lu (YL929).
First, we put the Artemis Nano board onto the car and hooked up a ToF sensor at the front of the car. The idea is to use the timing and the distance to calculate the speed and see distance. We test this in a living room.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/4/2.jpg)
Click **[here](https://youtu.be/9HhGui_mSuE)** if the video does not show.
<div class="video-container">
  <iframe width="640" height="360" src="https://youtu.be/9HhGui_mSuE" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>
Click **[here](https://youtu.be/p5TXN7i8xps)** if the video does not show.
<div class="video-container">
  <iframe width="640" height="360" src="https://youtu.be/p5TXN7i8xps" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

After data captured, I put it into excel and generate a curve.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/4/3.jpg)
Use the formula speed = distance/time I get, **`(4044-1154)mm / (1428-641)ms = 2.89m / 0.787s = 3.672 m/s`**.
The speed range is [0, 3.672] m/s.

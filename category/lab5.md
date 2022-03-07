---
layout: category
title: Lab 5
---

# Open Loop Control

## 0. Objective
Change from manual to open loop control, make the car able to execute a pre-programmed series of moves, using the Artemis board and two dual motor drivers.

## 1. Prelab
### Pins use for control on the Artemis
Since I need the Pulse-width modulation (PWM) to control motor drivers, according to the **[Artemis Nano Schematic](https://cdn.sparkfun.com/assets/5/5/1/6/3/RedBoard-Artemis-Nano.pdf)**, those pins with `~` in front of it support PWM, therefore I pick **`A2, A3, 4 and A5`** for my motor control pins. Note: those pins start with `A` support Analog to Digital Converters (ADC).
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/5/1.jpg)

### We recommend powering the Artemis and the motor drivers/motors from separate batteries. Why is that?
Each motor driver can deliver 1.2 A per channel continuously (2 A peak) to a pair of DC motors. In this lab, I have two motor drivers connecting in parallel, if attach the Artemis in parallel together, there may not have enough amps to support motor drivers which can cause the battery be drink out faster.

###  Hook up the motor drivers
To get the maximum amps, need parallel couple the two inputs and outputs on each motor driver.

For inputs, I use a tiny shunt jumper to connect B2_IN and A2_IN, then use a zero-ohm resistor to connect B1_IN and A1_IN. The reason for not using a bare wire here is to prevent a short circuit with B2/A2_IN when hooking motor drivers into the car and future debug. To make connections between the motor driver and Artemis Nano, I soldered a right-angle male pin on the B channel.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/5/2.jpg)

For outputs, there are two wires on each motor, green and white. I define the green wire to be connected to B1_OUT, and the white wire to B2_OUT and A2_OUT. Do the same thing for both motors. Also, use a zero-ohm resistor to connect B1_OUT and A1_OUT. 
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/5/3.jpg)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/5/4.jpg)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/5/5.jpg)

In the end, use a board-to-board connector pass through both VIN and GND pins to make two motor drivers as a stack.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/5/6.jpg)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/5/7.jpg)

Schematic diagram
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/5/Sketch_bb.jpg)

### Wiring
In order to pass sensor wires and two power wires (Artemis and motor drivers), I drilled a hole on the side of the battery holder, then pass all necessary wires to the battery holder by squeezing between two motors.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/5/8.jpg)

After wires passed the hole.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/5/9.jpg)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/5/10.jpg)

Finally, hook up sensors by using 3M 9448A Double Coated Tissue Tape.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/5/11.jpg)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/5/12.jpg)


## 2. What are reasonable settings for the power supply?
The DRV8833 dual motor driver datasheet states the operating voltage is **`2.7‌‌ V to 10.8 V`**.
I use the DC power supply and set 2.70 V along with maximum 2.0 A, connect it to one of the motor drivers.
Found out the lowerbound is around **`2.63 V`**.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/5/13.jpg)
Click **[here](https://youtu.be/h6ocp_dpemo)** if the video does not show.
<div class="video-container">
  <iframe width="640" height="360" src="https://youtu.be/h6ocp_dpemo" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

Next is the test on **`3.70 V`**, this is the LiPo battery voltage.
Click **[here](https://youtu.be/0qPUVuoCuus)** if the video does not show.
<div class="video-container">
  <iframe width="640" height="360" src="https://youtu.be/0qPUVuoCuus" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

Also, I tested **`5.00 V`**, but I don't want to go over 5 V in case to protect motor driver overheat, although the datasheet states maximum is 10.8 V.
Click **[here](https://youtu.be/J9-P-nVRHXY)** if the video does not show.
<div class="video-container">
  <iframe width="640" height="360" src="https://youtu.be/J9-P-nVRHXY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

## 3. Use analogWrite commands to generate PWM signals and show using an oscilloscope
The **`analogWrite()`** function ranges from 0 to 255, and I set it to 200.
{% highlight c linenos %}
void setup()
{
  pinMode(A2, OUTPUT);
  pinMode(A3, OUTPUT);
}

void loop()
{
  analogWrite(A2, 200);
  analogWrite(A3, 0);
}
{% endhighlight %}

To detect PWM signals, I attached the probe to the motor driver input pins.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/5/14.jpg)
Here is the demo video shows PWM signals on the oscilloscope with **`analogWrite(, 200)`**.
Click **[here](https://youtu.be/CUaEkdkWahI)** if the video does not show.
<div class="video-container">
  <iframe width="640" height="360" src="https://youtu.be/CUaEkdkWahI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

## 4. Take your car apart!

## 5. Run the motor in both directions

## 6. The lower limit for each motor still turns while on the ground

## 7. Move in a fairly straight line

## 8. Open loop, untethered control

## 9. Run only when it hears loud frequencies

## 10. Additional Tasks
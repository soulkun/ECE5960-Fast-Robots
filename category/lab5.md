---
layout: category
title: Lab 5
---

# Open Loop Control

## 0. Objective
Change from manual to open loop control, make the car able to execute a pre-programmed series of moves, using the Artemis board and two dual motor drivers.

## 1. Prelab
### What pins will you use for control on the Artemis? (It is worth considering both pin functionality and physical placement on the board/car).
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


## 3. Use analogWrite commands to generate PWM signals and show using an oscilloscope.

## 4. Take your car apart!

## 5. Run the motor in both directions.

## 6. The lower limit for each motor still turns while on the ground.

## 7. Move in a fairly straight line.

## 8. Open loop, untethered control.

## 9. Run only when it hears loud frequencies.

## 10. Additional Tasks
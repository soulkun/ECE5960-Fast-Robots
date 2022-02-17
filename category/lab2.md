---
layout: category
title: Lab 2
---

# Bluetooth

## 0. Objective
The purpose of this lab is to establish communication between your computer and the Artemis board through the bluetooth stack. We will be using Python inside a Jupyter notebook on the computer end and the Arduino programming language on the Artemis side.

## 1. Virtual Environment Setup
At first, I am running the Windows 7 SP1 with Python 3.8 (this is the latest supported version of Python) on my PC to configurate the virtual environment. Following the lab instructions on the webpage, successfully installed venv and jupyter nootbooks. I am able to active/deactive the virtual environment in the command prompt window.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/1.jpg)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/2.jpg)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/3.jpg)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/4.jpg)

Successfully started the jupyter lab on local address **`127.0.0.1`** on port **`8888`**.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/5.jpg)

However, when I try to go through the first code block in the the demo.ipynb, it gives me an error said **"Only Windows 10 is supported."**
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/6.jpg)

So second, I installed Windows 10 on the VmWare Workstation and go over again the environment setup part inside the virtual machine. This time the first code block passed. And I powered on Artemis Nano board, upload the ble_arduino code and successfully get the bluetooth MAC address. The MAC address is **`C0:07:E0:8D:9A:44`**. But the virtual machine cannot detect the Artemis Nano board, even I passed Intel bluetooth hardware to the VmWare.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/7.jpg)

At this point, I did multiple research and look up through the internet, using the external bluetooth adapter; also contacted on of the TA via Zoom meeting, but nothing help.
Finally, on the next day I install the Windows 10 natively on my laptop hard drive and re-build the virtual environment.
As shown below, it can detected the Artemis board and connect it.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/8.jpg)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/9.jpg)

## 2. Receive data from the Artemis board
Running this part code, my laptop can receive float and string from the Artemis board via bluetooth.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/10.jpg)


## 3. Send a command to the Artemis board
When send a **`PING`** command to the Artemis board, then use receive_string to capture return, gets a **`PONG`** string.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/11.jpg)

Send two integer to the Artemis board, the serial monitor on my PC can capture them.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/12.jpg)


## 4. Disconnect
As my laptop send a disconnect request, both my side and the Artemis board send out a message notification, shows successfully disconnected.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/13.jpg)

## 5. ECHO
First modify the Arduino code by learn from "PING-PONG" part.
{% highlight c linenos %}
case ECHO:

    char char_arr[MAX_MSG_SIZE];

    // Extract the next value from the command string as a character array
    success = robot_cmd.get_next_value(char_arr);
    if (!success)
        return;

    tx_estring_value.clear();
    tx_estring_value.append("ECHO: ");
    tx_estring_value.append(char_arr);
    tx_characteristic_string.writeValue(tx_estring_value.c_str());
    Serial.println(tx_estring_value.c_str());

    break;
{% endhighlight %}

Then in the jupyter notebook, use CMD.ECHO to test, successfully get the echo back!
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/ECHO_back.jpg)

## 6. SEND_THREE_FLOATS
First modify the Arduino code by learn from "SEND_TWO_INTS" part.
{% highlight c linenos %}
case SEND_THREE_FLOATS:
    /*
      * Your code goes here.
      */
    float flt_a, flt_b, flt_c;
    // Extract the next value from the command string as an float
    success = robot_cmd.get_next_value(flt_a);
    if (!success)
        return;

    // Extract the next value from the command string as an float
    success = robot_cmd.get_next_value(flt_b);
    if (!success)
        return;

    // Extract the next value from the command string as an float
    success = robot_cmd.get_next_value(flt_c);
    if (!success)
        return;

    Serial.print("Three Floats: ");
    Serial.print(flt_a);
    Serial.print(", ");
    Serial.print(flt_b);
    Serial.print(", ");
    Serial.println(flt_c);
    break;
{% endhighlight %}

In the jupyter notebook side, send one negative float and two positive floats, successfully get all them back!
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/Three_floats_back.jpg)

## 7. Difference between float recieving methods
Both of methods does the same job, receive a float number from the Artemis via the bluetooth and represent it on the screen, and both of them are reading byte array. However, in C programming language, float uses 4 bytes fixed size, while string using **number_of_char+1** size (total chars plus one string terminator \0), which size is vary. Since on Arduino side we are using Estring, which is based on C string. If the float we try to sending over is extremely large, this could cause buffer overflow and data corrupt on the receiver side. Thus, use receive_float may be a wise choice.

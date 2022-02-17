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
In this example, load the example file located at **`File-->Examples-->PDM-->Example1_MicrophoneOutput`** then upload to the board. The Artemis Nano board is using a Pluse-Density Modulation microphone. Open the serial monitor, it continuously shows the loudest frequency currently captured. One thing I noticed, when my environment is silent, the serial monitor shows the loudest frequency is around **20015 Hz**...

To test, I use my iPhone app to generate serval different frequencies, and finally I use my PC to generate a 16kHz sound, it seems this PDM microphone is pretty precise.
Click **[here](http://www.youtube-nocookie.com/embed/88ZAxnkcrFo)** if the video does not show.

<div class="video-container">
  <iframe width="640" height="360" src="http://www.youtube-nocookie.com/embed/88ZAxnkcrFo" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

## 7. Additional Task
Here is my solution for this special task. First, I need to light up the LED, so inside the setup function, I add the following.

{% highlight c linenos %}
pinMode(LED_BUILTIN, OUTPUT);
{% endhighlight %}

Second, I need to determine when to turn on the LED and turn it off. By testing multiple times, I found out the whistle sound is around **2000~3000 Hz**. Then I goes into printLoudest function, and at the end of this function, I add a return statement inorder to return the current frequency.

{% highlight c linenos %}
return ui32LoudestFrequency;
{% endhighlight %}

Also, I need to modify the function definition to specify the type of my return value.

{% highlight c linenos %}
uint32_t printLoudest(void)
{% endhighlight %}

In the loop function, I use a variable called freq to save the current frequency value that returns from the printLoudest.

{% highlight c linenos %}
uint32_t freq = printLoudest();
{% endhighlight %}

Finally, use the if-else statement to see if the current frequency is between **2000~3000 Hz**, turn on the LED if so, otherwise turn it off.

{% highlight c linenos %}
if(freq >= 2000 && freq <= 3000)
  digitalWrite(LED_BUILTIN, HIGH);
else
  digitalWrite(LED_BUILTIN, LOW);
{% endhighlight %}

Click **[here](http://www.youtube-nocookie.com/embed/I5yo20A9p-E)** if the video does not show.
<div class="video-container">
  <iframe width="640" height="360" src="http://www.youtube-nocookie.com/embed/I5yo20A9p-E" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>
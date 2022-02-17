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

Successfully started the jupyter lab on local address 127.0.0.1 on port 8888.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/5.jpg)

However, when I try to go through the first code block in the the demo.ipynb, it gives me an error said "Only Windows 10 is supported."
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/6.jpg)

So second, I installed Windows 10 on the VmWare Workstation and go over again the environment setup part inside the virtual machine. This time the first code block passed. And I powered on Artemis Nano board, upload the ble_arduino code and successfully get the bluetooth MAC address. The MAC address is C0:07:E0:8D:9A:44. But the virtual machine cannot detect the Artemis Nano board, even I passed Intel bluetooth hardware to the VmWare.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/7.jpg)

At this point, I did multiple research and look up through the internet, using the external bluetooth adapter; also contacted on of the TA via Zoom meeting, but nothing help.
Finally, on the next day I install the Windows 10 natively on my laptop hard drive and re-build the virtual environment.
As shown below, it can detected the Artemis board and connect it.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/8.jpg)
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/2/9.jpg)

## 2. Hook the Artemis board up to a computer
Before plug into to my PC, I noticed this board is running in +3.3V, but the USB output voltage is +5V. This concern solved by located board [Eagle Files](https://cdn.sparkfun.com/assets/f/e/c/9/c/RedBoardArtemisNano.zip), the below picture shows there is a power source selector on the board, both USB and battery input are fed to VIN, and VIN connects to a 3.3V regulator to provide correct voltage to the Artemis Nano board.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/1/power.jpg)

When I plug the Artemis Nano board, the operating system does not recognize this hardware, by looking up the [Hookup Guide](https://learn.sparkfun.com/tutorials/hookup-guide-for-the-sparkfun-redboard-artemis-nano?_ga=2.157583226.1568895310.1643606097-567105487.1643313494), I found the [CH340 Drivers](https://learn.sparkfun.com/tutorials/how-to-install-ch340-drivers). After installing the required drivers, the Arduino IDE is able to detect the correct COM ports.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/1/USB-SERIAL.jpg)

## 3. Blink it Up!
This part is pretty easy, just load the sample code and upload to the Artemis Nano board. I use baud rate **460800**. Below video shows the internal blue LED flashing in 0.5Hz.
Click **[here](http://www.youtube-nocookie.com/embed/njwVnxOrFAU)** if the video does not show.

<div class="video-container">
  <iframe width="640" height="360" src="http://www.youtube-nocookie.com/embed/njwVnxOrFAU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

## 4. Serial
Simple as the previous, load the sample code and upload to the board. Need to be careful set the baud rate to **115200**. Once upload to the board, use **`Tools-->Serial Monitor`** to send and view messages.
Click **[here](http://www.youtube-nocookie.com/embed/CPWnKTZDggU)** if the video does not show.

<div class="video-container">
  <iframe width="640" height="360" src="http://www.youtube-nocookie.com/embed/CPWnKTZDggU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

## 5. Analog Read
This example shows how to read analog data. First, load the example file, on my Arduino IDE, it locates at **`Files-->Examples-->Example02_AnalogRead`**. Then choose it and upload to the board. On line #56, 57 and 58 shows how to retrieve analog data;
{% highlight c linenos %}
int vcc_3 = analogReadVCCDiv3();    // reads VCC across a 1/3 voltage divider
int vss = analogReadVSS();          // ideally 0
int temp_raw = analogReadTemp();    // raw ADC counts from die temperature sensor
{% endhighlight %}

on line #60, the code shows "computed die temperature in deg F".
{% highlight c linenos %}
float temp_f = getTempDegF();       // computed die temperature in deg F
{% endhighlight %}
Open the serial monitor, we can see temp, VCC and VSS data updates in millisecond. To test it, I put mu thumb onto the board to makes it warm, and the temp value jumps from **33872 to 34232**.
Click **[here](http://www.youtube-nocookie.com/embed/WPtpbuohPgc)** if the video does not show.

<div class="video-container">
  <iframe width="640" height="360" src="http://www.youtube-nocookie.com/embed/WPtpbuohPgc" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>
  
## 6. Microphone Output
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
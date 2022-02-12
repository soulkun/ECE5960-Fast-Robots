---
layout: category
title: Lab 1
---

# The Artemis board

## 0. Objective
Get familiar with the Arduino IDE and the Artemis board, blink the LED, read/write serial messages over USB, display the output from the onboard temperature sensor, and measure the loudest frequency recorded by the Pulse Density Microphone.

## 1. Download and Install the Arduino IDE
I had the Arduino IDE installed before and updated to 1.8.19 version.
![](https://github.com/soulkun/ECE5960-Fast-Robots/raw/main/labs/1/Arduino_IDE.jpg)

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
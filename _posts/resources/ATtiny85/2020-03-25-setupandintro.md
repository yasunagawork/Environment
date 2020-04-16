---
layout: postsResource
title: 1.Setup and Introduction
discription: first post for learning ATtiny85!
category: resource
tag: attiny85

---

This series of resource will document my process of learning how to work with AVR. In this section, I will go over what I have done for the settting up my workflow and the toolchain for the experiments. There are many delails that I forget and also still unclear so I figured I should record it here so I can identify what I tryied and what I don't understand. I am following this book, [AVR Programming from MAKE by Elliot Williams](https://www.oreilly.com/library/view/make-avr-programming/9781449356484/). 
[Here](https://github.com/hexagon5un/AVR-Programming) is the link for the example files provided by the book. 


## A.Toolchain

Toolchain for working with AVR is as follows:

- text editor   >>   compilor   >>   flash programmer   >>   AVR

- <mark>Text editor</mark> is where we write our idea into code. Folloing the book, I will be writing C. I am using [SublimeText](https://www.sublimetext.com/) since it is something I am used to. 

- <mark>Compiler</mark> is where we turn human-readable C code into machine code for the AVR. Compiler I am using here is called avr-gcc that is included in a AVR developing environment (like WinAVR for Windows) for OSX called [CrossPack](https://www.obdev.at/products/crosspack/index.html) --- user manual [here](file:///usr/local/CrossPack-AVR-20131216/manual/index.html) CrossPack also contains AVRDUDE which is a software for reading and writing to the chip through the programmer. 

- <mark>Flash Programmer</mark> is a hardware that is a bridge between the computer and AVR. Flash Programmer send the information (compiled machine code) to the AVR's flash memory where they store. I am using this cute flash programmer called [AVR Pocket Programmer](https://learn.sparkfun.com/tutorials/pocket-avr-programmer-hookup-guide/all) from Sparkfun. We program AVR with SPI (serial prepheral interface) more on that [here](https://learn.sparkfun.com/tutorials/pocket-avr-programmer-hookup-guide/all)



## B.ATtiny85  -- [datasheet](http://ww1.microchip.com/downloads/en/DeviceDoc/Atmel-2586-AVR-8-bit-Microcontroller-ATtiny25-ATtiny45-ATtiny85_Datasheet.pdf)

<div class="dataimage2">
	<img src="{{site.baseurl}}/assets/img/resource/attiny85/attinychip.jpg">
</div>

I am using ATtiny85 for the experiments. ATtiny is a series of 8 bit microcontroller (meaning it process 8 bits of information at time). It is similar to the 8 bit microcontroller used in Arduino Uno [atmega328p](https://www.microchip.com/wwwproducts/en/ATmega328P). ATtinys are small in a family of the AVR mictocontrollers interms of amount of program memoory, number of available pins and functionality but they can be operated in a low power setup like solarcells. They are also very cheap ($1.20 each) and can be purchesed at [Digikey](https://www.digikey.com/product-detail/en/microchip-technology/ATTINY85-20PU/ATTINY85-20PU-ND/735469) They can function in a low voltage as low as 2.7V though ATtiny85V-10PU can operate in a voltage as low as 1.8V which is great for solar power application. 

<div class="dataimage2">
	<img src="{{site.baseurl}}/assets/img/resource/attiny85/pinout.png">
</div>

Above is the pinout for the ATtiny85 referencing the datasheet. There are 8 pins. 
- Pin 8 is VCC (3.3-5V)
- Pin 4 is Ground
- the rest is PB0 to PB5(PortB 0 to PortB 5). 
- Pin 1 (PB5) is also a RESET pin

Port is a group of pins that can be configured for input and output. There are different port like PortC and D on other microcontroller with more physical pins. PB0 to PB5 has other functions that we can access listed on the pin out image. I don't know what all of these mean for now but I'm assuming it will be clear as they are needed.


## C. Wiring and Connecting

Now it's a time to connect flash programmer to the ATtiny. The guide in [sparkfun website](https://learn.sparkfun.com/tutorials/pocket-avr-programmer-hookup-guide/all) provide good walkthough of how this things works and what we need to look out for.

1.First, we have to check if we will be powering the chip through the flashprogrammer. There is a tiny switch to select wheather or not I want to power the AVR with the programmer. If there is a power provided from other powersource while AVR is programmed, I will turn this switch to "no power". In my experiments, I will be powering ATtiny with programmer so I set it to "power target"

2.Now we wire up the ISP headers of programmer to the correcpond pins on ATtiny85. There are 6 wires required for communication:

- VCC (power)
- GND (ground)
- Reset 
- MOSI (master out slave in —— aka parent out child in)
- MISO (master in slave out —— aka parent in child out)
- SCK (serial clock)

You can find which pin on ATtiny85 corresponds to the pins on the programmer by referencing the datasheet pinout image. 

- Pin1 Reset
- Pin2 ---
- Pin3 ---
- Pin4 GND
- Pin5 MOSI
- Pin6 MISO
- Pin7 SCK
- Pin8 VCC

Here is what it looks like connected to ATtiny85 (ISP connector view from above)

<div class="dataimage2">
	<img src="{{site.baseurl}}/assets/img/resource/attiny85/wiringisp.png">
</div>

## D. CrossPack and Initialization

Once you got the CrossPack installed, you can create the directory for your projects. Follow [Getting Started](file:///usr/local/CrossPack-AVR-20131216/manual/gettingstarted.html). Once you made the directory for the projects, you can create the project with a template by typing "avr-project nameOfProject" in the terminal.
Now there are two file created in the project folder.

Project folder<br>
- └──firmware
  -   ├── main.c<br>
  -   └── Makefile

Before we get into the coding, we want to make sure we can connect to the ATiny. We will be using AVRDUDE to communicate with the mictocontrollers. AVRDUDE is part of the CrossPacks package so if you have CrossPack installed you should already have them. You can check the presence of AVRDUDE by typing "avrdude" in terminal and it will give you your version and the options. 

<div class="dataimage2">
	<img src="{{site.baseurl}}/assets/img/resource/attiny85/avrdudeoption.png">
</div>

<a href="https://www.ladyada.net/learn/avr/avrdude.html">Here</a> << is a good article/ tutorial from adafruit on command and use of AVRDUDE.

Now you can type:
- avrdude -p attiny85 -c usbtiny
- -p spesifies your chip and -c specified your programmer 

This will initialize the AVR and get them to be ready to recive the code. It will also list out:
- device signature
- status of fuse

<div class="dataimage2">
	<img src="{{site.baseurl}}/assets/img/resource/attiny85/avrdudeinitial.png">
</div>

If everything is good it will say "thank you."
Conitnue to next step for flashing the first program-->







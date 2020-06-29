---
layout: postsResource
title: 2.Fisrt Program
discription: first code
category: resource
tag: attiny85

---

Steps:

- [A. Configuring Makefile](#makefile)
- [B. make flash](#makeflash)
- [C. Code](#code)
- [D. DDRB and PORTB](#ddrbandportb)
- [E. Conclusion](#conclusion)

---



## A.Configuring Makefile <a name="makefile"></a>

Before we can send our test program to AVR, we need to cinfigure the Makefile to specific situations we are in. Makefile is a file that will contains spesification of our project that give instructions to the compiler. So when we translate our code from C to machine code, Makefile will bring together all the library files we reference and put it together according to our project specification. So we have to change Makefile for every project and configure them. 

Since CrossPack provide a skleton of Makefile when we create a project. I am working from the CrossPack Makefile instead of one that was provided with the book. Here are a few things we have to change in the file:

- DEVICE     --> spesify the AVR device you compile for
- CLOCK      --> Target AVR clock rate in Hertz
- PROGRAMMER --> opptions for what programmer we are using
- FUSES      --> Fuse setting for configuring the AVR device (explained below)

So my setting looks like this.

<div class="dataimage2">
	<img src="{{site.baseurl}}/assets/img/resource/attiny85/makefileconfig1.png">
</div>

Fuses are bits in the chip that are reserved for cinfiguring the chip for the speed they run at and how they act. <a href="https://www.ladyada.net/learn/avr/fuses.html"> Here </a><< is another great article from adafruit on fuse setting in AVR. 

There are calculator online that helps us generate these values acording to our needs. I used <a href="http://eleccelerator.com/fusecalc/fusecalc.php?chip=attiny85"> this calculter</a> to generate my fuse bits values. This website you can save your setting as a perma-link so you can comeback to it! Link for my setting is <a href="http://eleccelerator.com/fusecalc/fusecalc.php?chip=attiny85&LOW=62&HIGH=DF&EXTENDED=FF&LOCKBIT=FF"> Here</a>

Only thing that is important in my setting is here

<div class="dataimage2">
	<img src="{{site.baseurl}}/assets/img/resource/attiny85/fuse.png">
</div>

Basically my setting is intirely a default setting when you select attiny85 in a dropdown menu. You can select a clock setting from the dropdown menu to configure your AVR to run in defferent speed but it is important note there are internal clock and external clock. ATtiny has a internal clock that runs 8MHz on a default that everything is referensing. If you set to reference external clock, you have to have another component to keep time.
Important thing here is that in the setting I have, section for "devide clock by 8 internally" is checked. This means that internal clock 8 Mhz is devided by 8 which is 1Mhz. This is why I have my CLOCK setting in the Makefile to 1000000 (1MHz).

---

## B. make flash <a name="makeflash"></a>

Now we are ready to send the code to the ATtiny! First I will make sure that process will work so I will try to send an empty example code to the chip. Main C code is stored in main.c file. From the terminal window cd into your directory of the firmware file of your project (wherever you have your project stored)

- cd (your directory)
- cd /Users/work/Documents/AVR/test/firmware

Than you can type "make flash" to build the file and send them to the chip.

<div class="dataimage2">
	<img src="{{site.baseurl}}/assets/img/resource/attiny85/makeflash.png">
</div>

If everyting goes well, 3 more files are created in the firmware folder

<div class="dataimage2">
	<img src="{{site.baseurl}}/assets/img/resource/attiny85/firmwarefile.png">
</div>
 
As I understand, .o (object) file and .elm file is what my C code is being translated to and compiler does one more step of translation to create a .hex file which gets read by the AVR. [Here](https://ccrma.stanford.edu/~juanig/articles/wiriavrlib/AVR_GCC.html) is a wright up on this.

Here is what the .hex file of blink sketch looks like:

<pre>
:100000000EC015C014C013C012C011C010C00FC064
:100010000EC00DC00CC00BC00AC009C008C011241E
:100020001FBECFE5D2E0DEBFCDBF02D018C0E8CF03
:10003000BB9A88E088BB2FE936E891E02150304038
:100040009040E1F700C0000018BA2FE936E891E0CF
:10005000215030409040E1F700C00000EBCFF89411
:02006000FFCFD0
:00000001FF
</pre>

 From reading the feedback from AVRDUDE when I ran "make flash" command, we can see the steps of operation below,

 - 1- avr-gcc translate the main.c file 
 - 2- check the size of the program
 - 3- initilize the chip
 - 4- erace the chip
 - 5- reading main.hex file
 - 6- write to the flash memory
 - 7- varify the data written
 - 8- done.  thank you.  (bow)

Now we can move on to actually writing a code!

---


## C. Code <a name= "code"></a>

The structure of code for microcontrollers is described in a book as this:

<pre>

[preamble & includes]
[possibly some function definitions]
int main(void){
	[chip initializations]
	while(1) { [event loop]
		[do this stuff forever]
	}
}

</pre>

This is pretty similar to the structure for programming arduino. Code runs from top to buttom and there are part that runs once when the chip is turned on and there is a loop that keeps on looping as long as the chip is powered. Here is a image of the structure to visualize this structure. 

<div class="dataimage2">
	<img src="{{site.baseurl}}/assets/img/resource/attiny85/code_structure.png">
</div>

I really wish there is a way to color sections of code so it is easier to see and concepturalize them as a structure. 

<mark>Comment, preamble, include</mark> section is for managing things like dependencies for library or globabl variables and other tools we need to include. It is a reference for the compiler to output the machine code. So this part of code (as I understand) is not going to be stored on the AVR chip itself.

<mark>Functions</mark> is a sets of operation as a unit you can call in a main loop of the code. It is a way to modulary build your code and make it flexile and organized.

<mark>main()</mark> 

<mark>Chip initialization</mark> - this is a section that runs once whent the chip is powered. Mainly it configures different pins and prepare the chip for the kind of task we are about to do. It is like a taking out some tools on your workbench and getting ready for a things you want to make. 

<mark>while(1) loop</mark> 

<mark>main loop</mark> - This is where the action happens. Whatever in here will continually loop after chip initialization and until there is no power.



And here is the first blinking LED sketch


<div class="gist">
<script src="https://gist-it.appspot.com/github.com/lucasharoldsen/AVR/blob/master/1_digitalOutput_bitTwidling/blink/firmware/main.c"></script>
</div>

---


## D. DDRB and PORTB <a name="ddrbandportb"></a>

It took me a little bit to concepturalize the relationship between physical pins on the chips, ports and idea of registers. Here are some key points. It was easier for me to think about these concepts on a ATmega328p chip since it had more than a one banks.

### Bank of Pins
On microcontrollers there are "banks of pins" (ex. PORTB, PORTC, PORTD) they are group of pins in 8 (not always) that have a name like PB0 ~ PB7 (PB0 being first pin in PORTB and PB7 being 8th pin on the PORTB) These pins are physically located in a chip. For instance, on ATmega328p

<div class="dataimage2">
	<img src="{{site.baseurl}}/assets/img/resource/attiny85/bank_atmega328.png">
</div>

you can see that banks of pins are placed on different location of pins. So here PB2 is located on pin 16 on the chip. In a code you refer to PB2 to use the pin. In ATtiny85, the package is so small that we only have one bank of pins PORTB. PB2 is located on pin 7 on ATtiny85. 

<div class="dataimage2">
	<img src="{{site.baseurl}}/assets/img/resource/attiny85/pinout_portb.png">
</div>


### DDRB (Data Direction Register Bank B)

DDRB was in the example code above in the initialization section. This is short for Data Direction Register Bank B. So if you were trying to use bank C it would be DDRC. In a CPU of microcontroller there are place called registers where small amounts of space of memory is dedicated for configuring the body or conducting a specific operation. Data Direction Register is a reister in a microcontroller that can configure the specified pins to be used for INPUT or OUTPUT. By the default, they are configured to be INPUT to to use them as an output, we have to tell microcontroller that we want to do so. 

It is just like pinMode() in Arduino code. We are preparing the pin we are about to use.

The way to set a bit in a register is a another conceptural leap but fascinating. First if we look at the datasheet of ATtiny85, we can find Register Description on page 64. Here we can find which bit correspond to which pin of the bank. There are 8 bit on a register and each bit is turned high and low with transistors. By writing high of the corresponding redister bit, we can configure specific pin for the output. 

<pre>
DDRB

 bit7  bit6  bit5  bit4  bit3  bit2  bit1  bit0 

| PB7 | PB6 | PB5 | PB4 | PB3 | PB2 | PB1 | PB0 |

</pre>

So if we want to configure PB3 (pin 7 on a chip) to the output, we can write,

<pre>DDRB = 0b00001000</pre>

First 0b indicates that what follows is a binary number, and 00001000 is what we write. We are writing High (1) to the 4th bit from the right (bit3, PB3) mirroing the register description above. One thing to note here is that because we are writing whole 8 bit to the register, we are also writing low on all the other pins. It is fine for this project since we are only using one pin but as proect gets more complicated we need a way to flip the bit we want with out disturbing other bits. 

[Here is a great article on bit manipulation](http://www.rjhcoding.com/avrc-bit-manip.php)

Instead of writing whole 8 bit, I shifted the bit in using OR operation.

<pre>
DDRB = 0b00001000  

becomes,

DDRB |= (1 << PB3)
</pre>

### PORTB (Port B Data Register)

Now that we have configured our pin for output, we can send the voltage out by writing to PORTB. It works in a same way as DDRB that we can just write a bit high for the corresponding pin we are using. 

<pre>
PORTB |= (1 << PB3) // same as PORTB = 0b00001000
</pre>

This turns PB3 high.

<pre>
PORTB &= ~(1 << PB3) 
</pre>

And this turns the bit off. 

[Here is more in depth article on port manipulation](https://www.instructables.com/id/ATTiny-Port-Manipulation/)

---

## E. Conclusion <a name="conclusion"></a>

Up to this point code structure and idea somewhat resemble Arduino structure I know. I can see how things are packaged to be more legible and clearer. Although, undrstanding register and writing to the register really make the programming alot closer to the feeling of physically turning transistor in the microcontroller on and off. Way microcontroller deals with bit manipulation make the process feel alot more granural. 











---
layout: postsResource
title: 2.Fisrt Program
discription: first code
category: resource
tag: attiny85

---

## A.Configuring Makefile

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

## B. make flash

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
 
 I am not sure what do main.o and main.elf do, but from reading the feedback from AVRDUDE when I ran "make flash" command, we can see the steps of operation below,

 - 1- avr-gcc translate the main.c file 
 - 2- check the size of the program
 - 3- initilize the chip
 - 4- erace the chip
 - 5- reading main.hex file
 - 6- write to the flash memory
 - 7- varify the data written
 - 8- done.  thank you.  (bow)

Now we can move on to actually writing a code!


## C. Code

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



## D. DDRB and PORTB

It took me a little bit to concepturalize the relationship between pins on the chips, ports, and bi












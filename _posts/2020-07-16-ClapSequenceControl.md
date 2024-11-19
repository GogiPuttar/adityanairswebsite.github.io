---
layout: post
title:  "Home Automation with Clap Sequence Control"
categories: [C++, Signal Processing, Circuit Design
]
image: assets/images/Clap.gif
featured: false
hidden: false
---

C++, Signal Processing, Circuit Design

<br>

<div align="center"><iframe width="452" height="804" src="https://www.youtube.com/embed/Z5eeQXlyhH8" title="Clap sequence sensitive room light - Specificity in clap sequence" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe></div>

<br>

## Overview

This is a clap sequence sensitive home automation prototype that switches a lamp on and off. 
The C++ code on an Arduino Nano triggers when two loud bursts are heard with a short delay between them. 
It also takes a running average of the ambient noise to dynamically adjust how loud a clap "should" be.
The main accomplishments of this project are:

1. Can control any mains voltage appliance only by clapping.
2. Only registers a sequence of two claps with a short delay, and within some tolerance.
3. Adjusts for ambient noise to not give false positives.
4. Has debouncing protection to prevent false positives from a series of loud noises.
5. Allows manual control with a button.

<br>

<div align="center"><h2> <a href="https://github.com/GogiPuttar/Clap-Sequence-Sensor" target="_blank">View it on Github ⇗</a></h2></div>

<br>

## Personal Motivation

When the pandemic hit, I was in my first year of undergrad.
Despite my newfound enthusiasm for building circuits (thanks to my friend gifting me an Arduino kit that changed my life!), I was stuck at home with limited resources.
To entertain myself I would take apart and study the PCBs of stuff like bug zappers, oxygen sensors, lasers etc. while slowly building up an inventory of bought/scavenged electronic parts.
My mom finally directed me in a more constructive direction by asking me to build something that would let her turn the lights off without getting out of bed... and here we are!

<br>

## Hardware

The hardware consists of an `LM 393 sound sensor`, a `5V relay` with a `flyback diode`, a `2N3906 npn transistor` as a switch, and an `Arduino Nano`.
There is also a `50kΩ potentiometer` which can be used to adjust the sensitivity of the device i.e., if there's too much background noise, make the device less sensitive.  

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/ClapSequence-Board.png?raw=true"  width="45%"/>
<img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/ClapSequence-Schematic.png?raw=true" width="45%"/>
<figcaption>Fig. 1. The device with its schematic.</figcaption>
</figure>

<br>

## Conclusion

This device works extremely well, even over long periods of time. 
It does exactly what its supposed to and is very good at filtering out false positives like my dog barking, people talking, the TV running, and construction work outside of the house. 
Eventually, I ended up uninstalling it because it needed to be plugged in constantly, which would interfere with charging stuff.
Also, sometimes it is more convenient to get out of bed and just flip a switch, than clap your hands and wake half the house up!

<br>




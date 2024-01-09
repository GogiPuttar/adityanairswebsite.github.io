---
layout: post
title:  "Ploughing through Clutter: Robust Non-Holonomic Motion Control"
categories: [Python, Controls, Nonlinear Optimization, Dynamic Systems, Manipulation, Simulation, PyBullet, Wheeled Locomotion

]
image: assets/images/Ploughing.gif
featured: false
hidden: false
---

Python, Controls, Nonlinear Optimization, Manipulation, PyBullet, Wheeled Locomotion

<br>

## What problem does this solve?

Using PID control to track a moving target is inherently unstable on an under-actuated non-holonommic robot such as this one. Ultimately, any obstacles that come in the way can create errors so large, that they're impossible to recover from. 

<p align = "center">
<video width="40%" controls loop autoplay muted>
    <source src="https://github.com/GogiPuttar/adityanairswebsite.github.io/assets/59332714/6664fca0-20e6-43be-904b-d0c549d0df81" type="video/mp4">
</video>
</p>

<br>

## Limit Cycles

My approach instead uses a time-invariant controller based on a non-linear oscillator. 

<p align = "center">
<video width="40%" controls loop autoplay muted>
    <source src="https://github.com/GogiPuttar/adityanairswebsite.github.io/assets/59332714/de2dfd7a-b608-468b-8a97-80053587c1b7" type="video/mp4">
</video>
</p>

<br>

### This Post is under construction 🛠️
Over the winter break, I will be adding more information about my personal and professional projects. 
If you are interested, please check back on Janurary 14th, 2024.

In the meanwhile, the post on [**Dexterous Manipulation through Virtual Reality**](https://adityanairs.website/DexterousManipulationThroughVR/) is complete and ready for viewing.




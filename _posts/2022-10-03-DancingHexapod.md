---
layout: post
title:  "Dancing Hexapod - Full SE3 Body Pose Control"
categories: [Python, Controls, Motion Planning, Simulation, Legged Locomotion, PyBullet, HEBI Daisy Hexapod, Data Structures

]
image: assets/images/CrabRaveAlt.gif
featured: true
hidden: true
---

Python, Motion Planning, Legged Locomotion, PyBullet, HEBI Daisy Hexapod

***To feel the rhythm of the robot, I recommend you turn the volume on! 🔊***

<div align="center">
<video width="75%" controls loop autoplay muted>
    <source src="https://github.com/GogiPuttar/adityanairswebsite.github.io/assets/59332714/f7d2aad3-1d4a-49ac-836a-8549b3f0fa4a" type="video/mp4">
</video>
</div>

<br>

## Introduction

$$
\begin{align*}
p(s) &= c_0 + c_1 u + c_2 u^2 + c_3 u^3\\
&= \sum_{k=0}^{3} c_k u^k
\end{align*}
$$

<br>

<div align="center"><h2> <a href="https://github.com/marmotlab/Yuna_PyBullet/blob/main/setup/yunaAnimatronics.py">View it on Github ⇗</a></h2></div>

<br>

## Personal Motivation

<br> 

## Full SE3 Body Pose Control

<br>

## Animatronic Motion

<br>

<div align="center">
<video width="65%" controls loop autoplay muted>
    <source src="https://github.com/GogiPuttar/adityanairswebsite.github.io/assets/59332714/7f364020-b06b-46a9-ac32-d0b2ca041a63" type="video/mp4">
</video>
</div>

<br>

<br>

### Catmull-Rom Splines

$$
p(s) = 
\begin{bmatrix}
    1 & u & u^2 & u^3\\ 
    \end{bmatrix} 

\begin{bmatrix}
    0 & 1 & 0 & 0\\
    -\tau & 0 & \tau & 0\\
    2\tau & \tau-3 & 3 - 3\tau & -\tau \\
    -\tau & 2 - \tau & \tau - 2 & \tau \\ 
    \end{bmatrix}

\begin{bmatrix}
    p_{i-2}\\
    p_{i-1}\\
    p_{i}\\
    p_{i+1}\\ 
    \end{bmatrix}
$$

<p align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/SplineTrajectory.png?raw=true" width="50%"/>
</p>

<p align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/SplineBlendingFunction.png?raw=true" width="50%"/>
</p>

## Success

### This Post is under construction 🛠️
Over the winter break, I will be adding more information about my personal and professional projects. 
If you are interested, please check back on Janurary 14th, 2024.

In the meanwhile, the post on [**Dexterous Manipulation through Virtual Reality**](https://adityanairs.website/DexterousManipulationThroughVR/) is complete and ready for viewing.




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

## Overview

$$
\begin{align*}
q(s) &= c_0 + c_1 u + c_2 u^2 + c_3 u^3\\
&= \sum_{k=0}^{3} c_k u^k
\end{align*}
$$

<br>

<div align="center"><h2> <a href="https://github.com/marmotlab/Yuna_PyBullet/blob/main/setup/yunaAnimatronics.py">View it on Github ⇗</a></h2></div>

<br>

## Personal Motivation

<br> 

## Full $$SE(3)$$ Body Pose Control

Provided sufficient forces, the standing legs of a legged robot can "manipulate" the ground in its body's frame, to any pose within a reachable workspace; thereby allowing body pose control in the ground frame. 
This is my ***complete derivation*** of Body Pose Control in full $$SE(3)$$. 
This can be applied to any standing legged robot / delta platform provided it has sufficient degrees of freedom and reliable IK.
In this case, the $$3$$ standing legs possess $$9$$ degrees of freedom which is greater than $$6$$ required for body pose control (kinematically redundant).
I have also derived an equivalent expression for ***velocity kinematics*** and a ***$$9 \times 6 $$ Body Pose Jacobian*** but I have excluded it here to save space since it was not directly utilized for this project.

Given the world frame $$a$$, the initial body frame (home configuration) $$b$$ and the changed body frame $$c$$. We define the body pose $$[x, y, z, \alpha, \beta. \gamma]^T$$ such that:

$$
T_{bc} = 
\begin{bmatrix}
    &  &  & x\\
    & R_z(\gamma)R_y(\beta)R_x(\alpha)& & y\\
    &  &  & z\\
   0 & 0 & 0 & 1
\end{bmatrix},
$$

in the standard order, roll ($$\alpha$$) $$\rightarrow$$ pitch ($$\beta$$) $$\rightarrow$$ yaw ($$\gamma$$) in local frames.

Further, the positions of the three standing feet $$p_i: i = \{1,2,3\}$$, in the instantaneous body frame, are what we command to the IK-based controller to change the body pose. 
These feet positions are static in the world frame $$a$$. 
Therefore, changing these achieve a certain body pose amounts to only a simple reference frame shift of these points:

$$
p_{i,C} = T_{bc}^{-1} p_{i,B} : i = \{1, 2, 3\}.
$$

<br>

## Animatronic Motion

<br>

<div align="center">
<video width="65%" controls loop autoplay muted>
    <source src="https://github.com/GogiPuttar/adityanairswebsite.github.io/assets/59332714/7f364020-b06b-46a9-ac32-d0b2ca041a63" type="video/mp4">
</video>
</div>

<br>

### Catmull-Rom Splines

$$
q(s) = 
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
    q_{i-2}\\
    q_{i-1}\\
    q_{i}\\
    q_{i+1}\\ 
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




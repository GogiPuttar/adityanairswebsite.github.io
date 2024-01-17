---
layout: post
title:  "Optimized Torques for Load Carrying"
categories: [Research, Python, Legged Locomotion, Model-Predictive Control, Nonlinear Optimization, Controls, Dynamic Systems, Simulation, PyBullet, HEBI Daisy Hexapod, Data Structures
]
image: assets/images/Torque-ControlWalking.gif
featured: true
hidden: true
---

Research, Python, Legged Locomotion, Model-Predictive Control, Nonlinear Optimization, Dynamic Systems, HEBI Daisy Hexapod

<br>

<div align="center">
<video width="75%" controls loop autoplay muted>
    <source src="https://github.com/GogiPuttar/adityanairswebsite.github.io/assets/59332714/3b7aff70-48e1-4a76-ab71-8d593b03e74a" type="video/mp4">
</video>
</div>

<br>

## Abstract

The great terrain adaptability of legged robots,
their capacity to distribute forces among their legs and potential
to benefit from manipulating objects like carrying sensing
payloads, makes them particularly suited for load carrying applications. In this paper, we address the problem of optimizing
the joint-torques of articulated legged robots while performing
such physically strenuous tasks. We propose an inexpensive
optimization-based strategy designed to achieve arbitrary robot
configurations while minimizing the commanded torques, to
keep them within the physical limits of each actuator. Specifically, by first advancing our understanding of the relationship
between the ***ground reaction forces at the robot’s feet*** and
***torques in the robot’s joints***, we are able to pose a simple
***minimax optimization problem*** that can be solved online and
rapidly to yield ***optimized torque commands***. In doing so, our
approach is essentially able to manipulate the ground reaction
forces at the robot’s feet to increase/decrease internal moments
in the robot’s body, such that the final torques that must be
commanded by each actuator remains within its limits. We
experimentally validate our approach in static and walking
tests, where a series-elastic hexapod robot carries payloads of
varying weight, and show that our method results in lower
overall torques and lower average individual actuator effort.

<br>

## Personal Motivation

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/marmot.jpg?raw=true" width="70%"/>
<figcaption><b><em>(Some of) the MARMot Family :)</em></b></figcaption>
</figure>

<br>

## I. Introduction

Since legged robots typically approach their torque limits during physically demanding tasks, optimizing these actuator-torques becomes essential for smooth operation.
Such tasks are not only limited to walking on unstructured terrain, but also include manipulating large and/or heavy payloads on structured terrain which are required for applications such as construction, assembly, search and rescue etc.
Formally, the fundamental challenge for joint-torque optimization in legged robots can be stated as: preserving the high-level characteristics of walking, while simultaneously minimizing the load on/power consumed by the robot's actuators.
Since most legged robots are articulated (consist of rotational actuators), minimizing the load on the actuators is synonymous with minimizing the torques about the robot's joints.

Constraining the joint-torques of a legged robot within their limits is crucial for operations, and fully depends on the robot's configuration.
This is because the inability of even a single actuator to deliver its assigned torque can cause the robot's body pose to deviate from the desired one, potentially further rearranging the load and putting the remaining actuators at risk. 
This also indicates that a comprehensive understanding of the relations governing the robot's configuration, external wrenches (i.e. ground reaction forces and weight) and joint-torques, is first necessary, and can then be leveraged to optimize joint-torques.

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/YunaCarry.jpg?raw=true"   width="60%"/>
<figcaption><b><em>Fig. 1. 18-DoF series-elastic hexapod robot carrying a payload of 2 kg
(1kg yellow water bottle, plus 1kg metal weight) on top of its body.</em></b></figcaption>
</figure>

<br>

Given that the majority of research on legged walking is either concerned with feedforward or feedback motion tracking, or pure force control of the legs, optimal control of the ground reaction forces has generally been overlooked and have mainly been considered when preventing slippage.
In particular, the multi-dimensional ground reaction force space, together with the robot's constraints, presents an open avenue for optimal control via the supplementary degrees-of-freedom of the overactuated legged robot system.
Relevant to our work, and discovered that cockroaches employ a very relevant strategy while running, in that the front legs brake while the back legs accelerate.
This results in controlled frictional forces at the feet that effectively counteracts the torques in the insect's knees, reducing the effort required to run.
Building upon this intuition, in this work we develop a joint-torque optimization method for legged robots based on controlling the ground reaction forces at the feet, specifically for the task of carrying heavy loads.
While the robot is walking, at each time step, our optimization method takes the desired robot's configuration as input and optimizes joint-torques for every actuator, resulting in the most optimal ground reaction forces i.e., requiring minimal joint-torques to sustain that robot configuration.

Our method is designed to be applicable for any legged robot either statically standing, or walking in a statically stable gait.
Among these, given the numerous advantages that tripod gaits offer in terms of stability and strength, we test our strategy on an 18-DoF HEBI Daisy Hexapod robot walking in a tripod gait.
We achieve this gait using an open-loop Central Pattern Generator (CPG) motion controller, which is based on rhythmic motion controllers in biological systems, given its ease of parameterization and iterative nature.
However, our torque optimization method could be built on top of any arbitrary locomotion controller.

<br>

## II. Background

### A. Kinematics of a Legged Robot

In this work, we consider a bilaterally symmetrical hexapod robot, comprising a central body link and six articulated legs.
Each of the legs, indexed $i$ as in Fig. 2., consists of links connected by three actuated joints, namely the base, shoulder, and elbow. 
This grants each end-effector i.e., the foot, three degrees of freedom relative to the body.
For leg $$i$$, we represent the Jacobian matrix in the body frame as $$J_i(\theta_i) \in \mathbb{R}^{6 \times 3}$$ such that:

$$
\begin{equation}
    \mathcal{V}_i = J_i(\theta_i) \theta_i,
\end{equation}
$$

where $$\mathcal{V}_i \in \mathbb{R}^{6}$$ is the twist of the foot and $$\theta_i \in \mathbb{R}^{3}$$ are the joint angles. It also follows that:

$$
\begin{equation}
    \tau_{i} = J_i^{T}(\theta_i) \mathcal{F}_i,
\end{equation}
$$

where $$\tau_{i} \in \mathbb{R}^{3}$$ are the joint-torques required to generate a wrench $$\mathcal{F}_i \in \mathbb{R}^{6}$$ at the foot.
All vector quantities and transformations in this work consider the hexapod's body frame as the frame of reference, which is defined at the center-of-mass of the hexapod and oriented like in Fig.2.

Specific torques $$g_i(\theta_i) \in \mathbb{R}^3$$ must also be applied to counteract the significant weight of the various links and actuators of the robot.
That is, the torque commanded to the actuators not only includes the desired torque output $$\tau_{d_i}$$, depending on the application and external wrenches, but also accounts for the leg's weight and therefore is:

$$
\begin{equation}
    \tau_{i} = \tau_{d_i} + g_i(\theta_i).
\end{equation}
$$

In this work, we neglect the inertial and Coriolis contributions to torques as they are relatively much smaller.
However, note that these terms cannot generally be neglected for more agile/dynamic applications.

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/YunaLegs.jpg?raw=true"   width="70%"/>
<figcaption><b><em>Fig. 2. Hexapod’s body reference frame and orientation of indexed legs.</em></b></figcaption>
</figure>

<br>

### B. Hybrid Motion-Force Control

PID-based *motion control* in the joint-space can be used to output the desired torques needed to let the feet of the robot track a specific trajectory:
The desired torques to be tracked read:

$$
\begin{equation}
    \tau_{d_i} = M_i(\theta_i)\left(K_P \theta_{e_i} + K_D \dot{\theta}_{e_i} + \int_{}^{} K_I \theta_{e_i} dt\,\right),
\end{equation}
$$

where $$M_i(\theta_i) \in \mathbb{R}^{3 \times 3}$$ is the symmetric positive-definite mass matrix of the leg, and $$K_P$$, $$K_I$$, and $$K_D$$ are the respective PID control gains, which effectively translate joint-space errors into torque commands.
That is because, $$\theta_{e_i} = \theta_{d_i} - \theta_{i}$$ represents the error between the desired angle vector $$\theta_{d_i}$$ (obtained through inverse kinematics of the required leg pose $$X_{d_i} \in \mathbb{R}^6$$) and the current angle vector $$\theta_i$$.

On the other hand, feedforward *force control* offers a natural means to let foot $i$ exert a specific wrench $$\mathcal{F}_{d_i}$$ onto the environment:

$$
\begin{equation}
    \tau_{d_i} = J_i^{T}(\theta_i) \mathcal{F}_{d_i}.
\end{equation}
$$

<br>

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/YunaFoot.png?raw=true"   width="50%"/>
<figcaption><b><em>Fig. 3. Foot-ground interface during the stance phase of a leg.</em></b></figcaption>
</figure>

<br>

## Joint-Torque Optimization for Payload Carrying

### A. Hybrid Motion-Force Control in Hexapedal Locomotion

### B. Static Analysis and Ground Reaction Forces

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/YunaFull.png?raw=true"   width="70%"/>
<figcaption><b><em>Fig. 4. External forces acting on the robot in the static case.</em></b></figcaption>
</figure>

<br>

### C. Joint-Torque Estimation

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/YunaGRF.jpg?raw=true"   width="70%"/>
<figcaption><b><em>Fig. 5. Ground reaction forces and torque generated about an example
actuator (here, the front left elbow actuator).</em></b></figcaption>
</figure>

### D. Optimal Control

<br>

## Hardware Experiments

### A. Robot Description and CPG Walking

### B. Iterative Trust Region Optimization

### C. Experimental Procedure

<br>

## Results and Discussion

### A. Normalized Joint-Torque Measurements

### B. Joint-Torque Measurements

<br>

## Conclusion

<br>

### This Post is under construction 🛠️
Building a perfect portfolio from the ground up is a demanding process.
Over the coming weeks, I will be adding more information about my personal and professional projects. 
If you're interested, please check back on Janurary 31st, 2024.
Your curiosity is greatly appreciated `:)`

In the meanwhile, the posts on:
- [**Dexterous Manipulation through Virtual Reality**](https://adityanairs.website/DexterousManipulationThroughVR/), 
- [**Dancing Hexapod - Full SE3 Body Pose Control**](https://adityanairs.website/DancingHexapod/), 
- [**Navigation and Kalman Filter Localization of Self Driving Car**](https://adityanairs.website/SelfDrivingCar/), 
- [**Legged Robots for Object Manipulation: A Review**](https://adityanairs.website/LeggedRobotsForObjectManipulation/), 
- [**Home Automation with Clap Sequence Control**](https://adityanairs.website/ClapSequenceControl/), 
- [**Robot playing T-Rex Game**](https://adityanairs.website/TRexGame/), and
- [**Maze Solver**](https://adityanairs.website/MazeSolver/)

are complete and ready for viewing!


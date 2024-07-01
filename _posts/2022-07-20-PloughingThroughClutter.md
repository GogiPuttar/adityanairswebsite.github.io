---
layout: post
title:  "Ploughing through Clutter: Robust Non-Holonomic Motion Control"
categories: [Python, Controls, Nonlinear Optimization, Dynamic Systems, Manipulation, Simulation, PyBullet, Wheeled Locomotion, Turtlebot

]
image: assets/images/Ploughing.gif
featured: false
hidden: false
---

Python, Controls, Nonlinear Optimization, Manipulation, PyBullet, Wheeled Locomotion, Turtlebot

<br>

## What problem does this solve?

Traditional PID control for tracking a moving target is inherently unstable on an under-actuated non-holonomic robot such as this one. Ultimately, any obstacles that come in the way can create errors so large, they're impossible to recover from. 

<p align = "center">
<video width="40%" controls loop muted>
    <source src="https://github.com/GogiPuttar/adityanairswebsite.github.io/assets/59332714/6664fca0-20e6-43be-904b-d0c549d0df81" type="video/mp4">
</video>
</p>

<br>

## Limit Cycles

My approach instead uses a time-invariant controller based on a non-linear vector field. 

<p align = "center">
<video width="40%" controls loop muted>
    <source src="https://github.com/GogiPuttar/adityanairswebsite.github.io/assets/59332714/de2dfd7a-b608-468b-8a97-80053587c1b7" type="video/mp4">
</video>
</p>

<br>

### This Post is under construction 🛠️
Building a perfect portfolio from the ground up is a demanding process.
Over the coming weeks, I will be adding more information about my personal and professional projects. 
If you're interested, please check back on July 30th, 2024.
Your curiosity is greatly appreciated `:)`

In the meanwhile, the posts on:
- [**Search-and-Rescue Robot Dog: Autonomous Exploration and Visual SLAM with Unitree Go1**](https://adityanairs.website/SearchAndRescue/), 
- [**Complete Extended-Kalman-Filter SLAM Pipeline in C++ from Scratch**](https://adityanairs.website/SLAMpipeline/), 
- [**Dexterous Manipulation through Virtual Reality**](https://adityanairs.website/DexterousManipulationThroughVR/), 
- [**Optimized Torques for Load Carrying**](https://adityanairs.website/JointTorqueOptimization/),
- [**Dancing Hexapod - Full SE3 Body Pose Control**](https://adityanairs.website/DancingHexapod/), 
- [**Navigation and Kalman Filter Localization of Self Driving Car**](https://adityanairs.website/SelfDrivingCar/), 
- [**Mobile/Whole-body Manipulation with KUKA youBot**](https://adityanairs.website/MobileManipulation/),
- [**Legged Robots for Object Manipulation: A Review**](https://adityanairs.website/LeggedRobotsForObjectManipulation/), 
- [**TeleOperation of Robot Swarms**](https://adityanairs.website/TeleOperationOfRobotSwarms/), 
- [**Home Automation with Clap Sequence Control**](https://adityanairs.website/ClapSequenceControl/), 
- [**Robot playing T-Rex Game**](https://adityanairs.website/TRexGame/), and
- [**Maze Solver**](https://adityanairs.website/MazeSolver/)

are complete and ready for viewing!




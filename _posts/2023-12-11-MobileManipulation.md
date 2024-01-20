---
layout: post
title:  "Mobile/Whole-Body Manipulation with KUKA youBot"
categories: [MATLAB, Manipulation, Motion Planning, Controls, Simulation, Wheeled Locomotion, CoppeliaSim, KUKA youBot, Data Structures

]
image: assets/images/MobileManipulation.gif
featured: false
hidden: false
---

MATLAB, Manipulation, Motion Planning, Controls, CoppeliaSim, KUKA youBot

<br>

<div align="center">
<video width="80%" controls loop autoplay muted>
    <source src="https://github.com/GogiPuttar/adityanairswebsite.github.io/assets/59332714/a663013e-a913-4e6c-8d2e-881ea00691ce" type="video/mp4">
</video>
</div>

<br>

## Overview
This was the Capstone Project for the course: ***ME 449 Robotic Manipulation*** by ***Dr. Kevin Lynch*** at Northwestern University.
In this project, I wrote software that ***plans a trajectory*** for the end-effector of a KUKA youBot mobile manipulator (a mobile base with four mecanum wheels and a 5R robot arm), ***performs odometry*** as the chassis moves, and performs ***feedback control*** to drive the youBot to pick up a block at a specified location, carry it to a desired location, and put it down.

The final output of my software is a comma-separated value (csv) text file generated using a MATLAB script that specifies the configurations of the chassis and the arm, the angles of the four wheels, and the state of the gripper (open or closed) as a function of time. 
This specification of the position-controlled youBot was then tested on the CoppeliaSim simulator.
Here, the ODE physics engine was used to simulate contact dynamics and inertial forces.

**The program takes as input:**

- The initial resting configuration of the cube object (which has a known geometry), represented by a frame attached to the center of the object
- The desired final resting configuration of the cube object
- The actual initial configuration of the youBot.
- The initial configuration $$T_{se}$$ of the reference trajectory for the end-effector frame of the youBot 

**The program's output is:**

- A csv file which, when "played" through the CoppeliaSim scene, drives the youBot to successfully pick up the block and put it down at the desired location.
- A data file containing the 6-vector end-effector error as a function of time

<br>

## Personal Motivation

## Kinematics of the youBot



<figure align = "center">
    <img src="https://hades.mech.northwestern.edu/images/thumb/3/33/Yb-book.png/1200px-Yb-book.png" width="65%"/>
    <figcaption><em>Fig. 1. This figure illustrates the arm at its home configuration (all joint angles zero) and the frames {s}, {b}, {0}, and {e}. For the image on the right, joint axes 1 and 5 (not shown) point upward and joint axes 2, 3, and 4 are out of the screen. 
    <a href="https://hades.mech.northwestern.edu/index.php/File:Yb-book.png" target="_blank">[Image Source]</a></em></figcaption>
</figure>

<br>

## Dynamics Simulation

## Trajectory Generation

<figure align = "center">
    <img src="https://hades.mech.northwestern.edu/images/f/fc/Capstone-traj1.png" width="42%"/>
    <img src="https://hades.mech.northwestern.edu/images/6/68/Capstone-traj2.png" width="42%"/>
</figure>
<figure align = "center">
    <img src="https://hades.mech.northwestern.edu/images/2/2c/Capstone-traj3.png" width="42%"/>
    <img src="https://hades.mech.northwestern.edu/images/f/fe/Capstone-traj4.png" width="42%"/>
</figure>
<figure align = "center">
    <img src="https://hades.mech.northwestern.edu/images/f/f5/Capstone-traj5.png" width="42%"/>
    <img src="https://hades.mech.northwestern.edu/images/a/a2/Capstone-traj6.png" width="42%"/>
</figure>
<figure align = "center">
    <img src="https://hades.mech.northwestern.edu/images/4/4c/Capstone-traj7.png" width="42%"/>
    <img src="https://hades.mech.northwestern.edu/images/1/18/Capstone-traj8.png" width="42%"/>
    <figcaption><em>Fig. 2. The 8 segments of the KUKA youBot's trajectory. This picks the cube up and then places it at another location.
    <a href="https://hades.mech.northwestern.edu/index.php/Mobile_Manipulation_Capstone_2023" target="_blank">[Image Source]</a></em></figcaption>
</figure>

<br>

## Feedback Control

<figure align = "center">
    <img src="https://hades.mech.northwestern.edu/images/1/13/Yb-fbtest-new.png" width="65%"/>
    <figcaption><em>Fig. 3. This figure illustrates a test for your FeedbackControl and Jacobian pseudoinverse functions. The current robot configuration is shown, as well as the current end-effector configuration, the current reference end-effector configuration, and the reference end-effector configuration at the next time-step.
    <a href="https://hades.mech.northwestern.edu/index.php/File:Yb-fbtest-new.png" target="_blank">[Image Source]</a></em></figcaption>
</figure>

<br>

## Reference

1. [Mobile Manipulation Capstone 2023 - Northwestern Mechatronics Wiki](https://hades.mech.northwestern.edu/index.php/Mobile_Manipulation_Capstone_2023){:target="_blank" rel="noopener"}

### This Post is under construction 🛠️
Building a perfect portfolio from the ground up is a demanding process.
Over the coming weeks, I will be adding more information about my personal and professional projects. 
If you're interested, please check back on Janurary 31st, 2024.
Your curiosity is greatly appreciated `:)`

In the meanwhile, the posts on:
- [**Dexterous Manipulation through Virtual Reality**](https://adityanairs.website/DexterousManipulationThroughVR/), 
- [**Dancing Hexapod - Full SE3 Body Pose Control**](https://adityanairs.website/DancingHexapod/), 
- [**Optimized Torques for Load Carrying**](https://adityanairs.website/JointTorqueOptimization/),
- [**Navigation and Kalman Filter Localization of Self Driving Car**](https://adityanairs.website/SelfDrivingCar/), 
- [**Legged Robots for Object Manipulation: A Review**](https://adityanairs.website/LeggedRobotsForObjectManipulation/), 
- [**Home Automation with Clap Sequence Control**](https://adityanairs.website/ClapSequenceControl/), 
- [**Robot playing T-Rex Game**](https://adityanairs.website/TRexGame/), and
- [**Maze Solver**](https://adityanairs.website/MazeSolver/)

are complete and ready for viewing!




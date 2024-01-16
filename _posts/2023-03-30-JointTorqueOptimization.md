---
layout: post
title:  "Joint-Torque Optimization for Payload Carrying Operations"
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

## Overview

<p align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/ReviewPaper.jpg?raw=true"   width="90%"/>
</p>

<br>

## Personal Motivation

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

## Introduction

<p align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/ReviewPaper.jpg?raw=true"   width="90%"/>
</p>

<br>

## Background

A. Kinematics of a Legged Robot

B. Hybrid Motion-Force Control

<br>

## Joint-Torque Optimization for Payload Carrying

A. Hybrid Motion-Force Control in Hexapedal Locomotion

B. Static Analysis and Ground Reaction Forces

C. Joint-Torque Estimation

D. Optimal Control

<br>

## Hardware Experiments

A. Robot Description and CPG Walking

B. Iterative Trust Region Optimization

C. Experimental Procedure

<br>

## Results and Discussion

A. Normalized Joint-Torque Measurements

B. Joint-Torque Measurements

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


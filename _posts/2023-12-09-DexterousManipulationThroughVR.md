---
layout: post
title:  "Dexterous Manipulation through Virtual Reality"
categories: [Python, ROS2/ROS, Computer Vision, Manipulation, Motion Planning, Dynamic Systems, Controls, Simulation, Gazebo, Shadow Hands, Emika Franka Robot Arm, Intel RealSense, ABB Gofa Arm, Data Structures
]
image: assets/images/ShadowHands.gif
featured: true
hidden: true
---

Python, ROS2, Computer Vision, Manipulation, Motion Planning, Gazebo, Shadow Hands, Emika Franka Robot Arm, Intel RealSense, ABB Gofa Arm

<div align="center"><iframe width="80%" height="375" src="https://www.youtube.com/embed/_XrXWBiALow" title="Dexterous Manipulation through Virtual Reality" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe></div>

<br>

## Overview
The goal of this project was to design a teleoperation system which leverages object models in order to make the user's experience more intuitive. The idea is that a human operator can stack virtual rings in a simulated environment, all while getting instantaneous haptic feedback thanks to the local object models and low-latency simulation environment. The user's impact on the world is then replicated by an avatar robot, i.e. the robot does the same thing with the rings that the human user did. While we did not end up bridging the operator station and avatar station, we developed several key packages which will contribute to this end goal. These packages are outlined below, and more detailed information about how to run each package, etc. can be found in the READMEs of each individual package.

<div align="center"><h2> <a href="https://github.com/ME495-EmbeddedSystems/final-project-teleop">View it on Github ⇗</a></h2></div>

### What Problem Does This Solve?
Typically, a human operator and robot avatar pair involves live visual and tactile feedback, in that the operator can adjust their motion based on what they see/feel through the avatar. This sort of a setup is not very good in high latency networks which would be the case if the avatar is on a different continent, or maybe even in space. Our approach tries to solve this problem by:

 1. Simulating a virtual environment for the human operator and 
 2. Subscribing some intelligence to the robot avatar to adapt to the real world environment.

### Team Members
This team effort was part of the final project in the [ME 495 Embedded Systems]() course at Northwestern. I worked on this project with:
- Rohan Kota
- Luke Batteas
- Zach Alves
- Leo Chen

<br>

## Packages:

This projects consists of the following packages:
- [`teleop_tasks`](#teleop_tasks-package)
- [`teleop_haptics`](#teleop_haptics-package)
- [`teleop_sensing`](#teleop_sensing-package)
- [`teleop_visualization`](#teleop_visualization-package)
- [`teleop_avatar`](#teleop_avatar-package)
- [`teleop_interfaces`](#teleop_interfaces-package)
- [`teleop`](#teleop-package) (*work in progress*)

*Instructions on how to run/launch nodes in each package can be found within the READMEs of the individual packages.*

<br>

## `teleop_tasks` Package

The `teleop_tasks` package defines and launches virtual tasks, which can be completed by the user. We took two approaches to this:
- Programming some crude physics into __RViz__
- Using the physics engine that comes with __Gazebo__

### RViz:
The video below shows an example of a user completing a ring stacking task. The user can feel the mass of the rings due to the Franka robots attached to their hands. The HaptX gloves allow the user to feel the rings in their hands by activating the finger brakes and inflating the tactors when the user picks up a ring.

<div align="center"><iframe width="80%" height="375" src="https://github.com/ME495-EmbeddedSystems/final-project-teleop/assets/122302059/b09ece30-6af2-4751-981c-6e6976359e5a" title="RViz Simulation Demo" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe></div>

### Gazebo:
The video below shows a hand moving around in Gazebo. This hand can interact with the objects in the Gazebo world, and the hand's state is dictated by the hand of the human operator.

<div align="center"><iframe width="80%" height="375" src="https://github.com/ME495-EmbeddedSystems/final-project-teleop/assets/45044587/034b8f07-48cb-4aeb-b7dd-29035d9955ad" title="Gazebo Simulation Demo" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe></div>

<br>

## `teleop_haptics` Package

The `teleop_haptics` package can be used to provide haptic feedback to the user. There are 2 main forms of haptic feedback:

- __Kinesthetic Feedback__: Provided by a Franka robot attached to the user's wrist, the kinesthetic feedback allows a user to experience virtual masses when picking up objects.
- __Cutaneous Feedback__: Provided by the HaptX gloves, the cutaneous feedback allows a user to feel objects on their fingertips.

<br>

## `teleop_sensing` Package

The `teleop_sensing` package uses computer vision to calculate the locations of objects present in the avatar robot's workspace.

A demo of the package's ability to detect the positions of the rings is shown below:

<div align="center"><iframe width="80%" height="375" src="https://github.com/ME495-EmbeddedSystems/final-project-teleop/assets/7969697/c79e4f70-6d18-47e0-8438-9ad4425930bc" title="Computer Vision Demo" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe></div>

<br>

## `teleop_visualization` Package

The `teleop_visualization` package allows you to visualize the avatar's workspace in RViz.

<p align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/ShadowHandsRealLife.png?raw=true" height="450"   width="49%"/>
<img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/ShadowHandsRViz.png?raw=true" height="450"   width="49%"/></p>

<p style="text-align:left;">
    <strong>Real Life</strong>
    <span style="float:right;">
        <strong>RViz</strong>
    </span>
</p>

<br>

## `teleop_avatar` Package

***My primary contribution to the project was the `teleop_avatar` package.***<br> 
The `teleop_avatar` package controls the avatar robot to pick up objects and move them. In order to pick up an object, you can simply call a service specifying the object's id. There is also a service which allows you to pick up rings and place them on the peg by simply specifying the object's id.

Here is an example of the teleop_avatar package picking up rings and stacking them on a peg:

<div align="center"><iframe width="80%" height="375" src="https://github.com/ME495-EmbeddedSystems/final-project-teleop/assets/122302059/d2059ccf-5fb9-461d-afd6-dd741a69f29b" title="Manipulation Demo" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe></div>

<br>

## `teleop_interfaces` Package

The `teleop_interfaces` package contains all the custom messages and services used by the teleoperation system.

#### Custom Messages:
- FingerWrenches
- ObjectState

#### Custom Services:
- Grasp
- ExecuteTrajectory
- SetWrench

<br>

## `teleop` Package

The `teleop` package is a work in progress, but it is meant to bridge the operator and avatar stations and orchestrate the entire teleoperation experience. It will launch all the nodes necessary for the user to complete the task in a simulated environments, as well as all the nodes needed to run the avatar station. When a user begins moving objects in the simulated world, it will sense this and begin publishing the transforms of the object to the avatar station. Once the user is done moving the object, the nodes within this package will prompt the avatar station to begin executing the same trajectories with the real-life objects.




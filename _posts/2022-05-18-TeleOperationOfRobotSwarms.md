---
layout: post
title:  "TeleOperation of Robot Swarms with ROS"
categories: [Python, ROS2/ROS, Motion Planning, Controls, Wheeled Locomotion, Jetson Nano, Data Structures
]
image: assets/images/TeleopSwarmAlt.gif
featured: false
hidden: false
---

Python, ROS, Motion Planning, Controls, Wheeled Locomotion, Jetson Nano

## Overview
Swarm Robotics explores the design of robot groups operating independently, guided by principles of swarm intelligence. My project focuses on implementing swarm control principles for two Firebird VI robots, allowing them to navigate an obstacle map collectively. The project embraces the challenges of fault tolerance, scalability, and flexibility inherent in swarm robotics, making it suitable for tasks like demining, search and rescue, and planetary exploration.

<br>

## Personal Motivation

<br>

## Abstracted Control
In the realm of swarm robotics, abstraction is essential, granting the swarm a level of intelligence to collectively accomplish tasks. My approach involves teleoperating a swarm of two Firebird VI robots with limited individual control. Instead, the swarm is maneuvered as a whole, capable of translation, rotation, and orientation changes. With a total of $$4$$ degrees-of-freedom, the swarm's flexibility allows it to configure itself in various orientations on the horizontal plane.

<br>

## Firebird VI Robots
The Firebird VI platforms serve as the foundation for the swarm, equipped with high-precision DC gear motors, ultrasonic range sensors, IR distance sensors, and more. These robots provide a versatile base for swarm robotics, combining indoor and outdoor mobility. While the 4-wheeled differential drive offers robustness on rough surfaces, it introduces challenges in motion accuracy, addressed through this innovative approach.

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/FirebirdVI.png?raw=true" width="50%"/>
<figcaption>Fig. 1. A Firebird VI robot.</figcaption>
</figure>

<br>

## ROS Integration
The project leverages the Robot Operating System (ROS), a middleware suite facilitating the development of robot software. ROS enables a graph architecture where nodes process sensor data, control, planning, and communication. Launch files and polymorphic scripts define the swarm's behavior, with each Firebird VI robot subscribing to topics for seamless communication within the swarm.

<br>

## Polymorphic Scripts and Swarm Control Algorithms
These polymorphic scripts, designed for flexibility, dynamically adjust based on input and robot identity. Swarm control algorithms like 'Square', 'Elongate' and 'Flatten' showcase the swarm's ability to split, trace patterns, and adapt to changing configurations. 

### Square Maneuver
The square functionality, for instance, orchestrates a synchronized movement to outline a square, demonstrating the collaborative nature of the swarm. This allows a swarm to passes around obstacles, in an otherwise sparesly populated environment.

<div align="center">
<video width="75%" controls loop autoplay muted>
    <source src="https://github.com/GogiPuttar/adityanairswebsite.github.io/assets/59332714/0f61cab0-0b81-4446-aeb2-996a49263181" type="video/mp4">
</video>
</div>
<div align="center">
<em><b>Obstacle avoidance: Distributed control for morphing around an obstacle.</b></em>
</div>

<br>

### Elongating and Flattening

<div align="center">
<video width="75%" controls loop autoplay muted>
    <source src="https://github.com/GogiPuttar/adityanairswebsite.github.io/assets/59332714/0f61cab0-0b81-4446-aeb2-996a49263181" type="video/mp4">
</video>
</div>
<div align="center">
<em><b>Elongates to go through a narrow gap, and then flattens out to original shape</b></em>
</div>

<br>

### Elongating and Flattening at an angle

<div align="center">
<video width="75%" controls loop autoplay muted>
    <source src="https://github.com/GogiPuttar/adityanairswebsite.github.io/assets/59332714/0f61cab0-0b81-4446-aeb2-996a49263181" type="video/mp4">
</video>
</div>
<div align="center">
<em><b>Versatile motion: Can even travel through a tunnel at a slant.</b></em>
</div>

<br>

## Conclusion
The Firebird VI robot's sensor array, coupled with ROS control, proves versatile for diverse applications. Navigating the challenges of swarm control required a strong foundation in ROS, evident in the creation of launch files and polymorphic scripts. However, the 4-wheel differential drive posed challenges in motion accuracy, suggesting potential improvements in robot configurations for future work.

### This Post is under construction 🛠️
Building a perfect portfolio from the ground up is a demanding process.
Over the coming weeks, I will be adding more information about my personal and professional projects. 
If you're interested, please check back on Janurary 31st, 2024.
Your curiosity is greatly appreciated `:)`

In the meanwhile, the posts on:
- [**Dexterous Manipulation through Virtual Reality**](https://adityanairs.website/DexterousManipulationThroughVR/), 
- [**Optimized Torques for Load Carrying**](https://adityanairs.website/JointTorqueOptimization/),
- [**Dancing Hexapod - Full SE3 Body Pose Control**](https://adityanairs.website/DancingHexapod/), 
- [**Navigation and Kalman Filter Localization of Self Driving Car**](https://adityanairs.website/SelfDrivingCar/), 
- [**Mobile/Whole-body Manipulation with KUKA youBot**](https://adityanairs.website/MobileManipulation/),
- [**Legged Robots for Object Manipulation: A Review**](https://adityanairs.website/LeggedRobotsForObjectManipulation/), 
- [**Home Automation with Clap Sequence Control**](https://adityanairs.website/ClapSequenceControl/), 
- [**Robot playing T-Rex Game**](https://adityanairs.website/TRexGame/), and
- [**Maze Solver**](https://adityanairs.website/MazeSolver/)

are complete and ready for viewing!




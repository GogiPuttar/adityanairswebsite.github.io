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

<br>

<div align="center">
<video width="85%" controls loop autoplay muted>
    <source src="https://github.com/GogiPuttar/adityanairswebsite.github.io/assets/59332714/cf68b973-d896-4529-8686-8b9e3ea964e2" type="video/mp4">
</video>
</div>
<div align="center">
<em><b>The operator can make the swarm autonomously change shape to travel through narrow tunnels, and morph around small obstacles.</b></em>
</div>

<br>

## Overview
Swarm Robotics explores the design of robot groups operating independently, guided by principles of swarm intelligence. My project focuses on implementing swarm control principles for two Firebird VI robots, allowing them to navigate an obstacle map collectively. The aim of this project was to create a ***human-in-the-loop swarm control algorithm, combining the abstraction of control offered by swarm intelligence with the robust maneuverability of teleoperation***. The project embraces the challenges of fault tolerance, scalability, and flexibility inherent in swarm robotics, making it suitable for tasks like demining, search and rescue, and planetary exploration.

<br>

## Personal Motivation
As an undergraduate researcher at the [Inspire Lab](https://universe.bits-pilani.ac.in/pilani/avinash/inspireLab) at BITS Pilani, our team developed various ROS packages for Single-Agent and Multi-Agent control, navigation, and sensing of mobile robots. As the head of this investigation, this project was my contribution to the pipeline.

<br>

## Abstracted Control
In the realm of swarm robotics, abstraction is essential, granting the swarm a level of intelligence to collectively accomplish tasks. My approach involves teleoperating a swarm of two Firebird VI robots with limited individual control. Instead, the swarm is maneuvered as a whole, capable of translation, rotation, and orientation changes. With a total of $$4$$ degrees-of-freedom, the swarm's flexibility allows it to configure itself in various orientations on the horizontal plane.

<br>

## Firebird VI Robots
The Firebird VI platforms serve as the foundation for the swarm, equipped with high-precision DC gear motors, ultrasonic range sensors, IR distance sensors, and more. These robots provide a versatile base for swarm robotics, combining indoor and outdoor mobility. While the 4-wheeled differential drive offers robustness on rough surfaces, it introduces challenges in motion accuracy, addressed through this innovative approach.

<br>

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/FirebirdVI.png?raw=true" width="40%"/>
<figcaption>Fig. 1. A Firebird VI robot.</figcaption>
</figure>

<br>

## ROS Integration
The project leverages the Robot Operating System (ROS), a middleware suite facilitating the development of robot software. ROS enables a graph architecture where nodes process sensor data, control, planning, and communication. Launch files and polymorphic scripts define the swarm's behavior, with each Firebird VI robot subscribing to topics for seamless communication within the swarm.

<br>

## Polymorphic Scripts and Swarm Control Algorithms
These polymorphic scripts, designed for flexibility, dynamically adjust based on input and robot identity (ROS namespace). Swarm control algorithms like 'Square', 'Elongate' and 'Flatten' showcase the swarm's ability to split, trace patterns, and adapt to changing configurations. 

### Square Maneuver
The square functionality, for instance, orchestrates a synchronized movement to outline a square, demonstrating the collaborative nature of the swarm. This allows a swarm to passes around obstacles, in an otherwise sparesly populated environment.

<br>

<div align="center">
<video width="75%" controls loop muted>
    <source src="https://github.com/GogiPuttar/adityanairswebsite.github.io/assets/59332714/2655f34d-a3d7-4993-8ba1-b5a28fa6e07c" type="video/mp4">
</video>
</div>
<div align="center">
<em><b>Obstacle avoidance: Distributed control for morphing around an obstacle.</b></em>
</div>

<br>

### Elongating and Flattening
The operator can make the swarm orient itself in a long straight line which is suitable for traveling through narrow tunnels, after which the swarm can restore itself to its initial configuration.

<br>

<div align="center">
<video width="75%" controls loop muted>
    <source src="https://github.com/GogiPuttar/adityanairswebsite.github.io/assets/59332714/cf68b973-d896-4529-8686-8b9e3ea964e2" type="video/mp4">
</video>
</div>
<div align="center">
<em><b>Elongates to go through a narrow gap, and then flattens out to original shape</b></em>
</div>

<br>

### Elongating and Flattening at an angle
This strategy can even work for tunnels at arbitrary angles by initially spinning every individual robot. 

<br>

<div align="center">
<video width="75%" controls loop muted>
    <source src="https://github.com/GogiPuttar/adityanairswebsite.github.io/assets/59332714/0f61cab0-0b81-4446-aeb2-996a49263181" type="video/mp4">
</video>
</div>
<div align="center">
<em><b>Versatile motion: Can even travel through a tunnel at a slant.</b></em>
</div>

<br>

## Conclusion
The Firebird VI robot's sensor array, coupled with ROS control, proves versatile for diverse applications. Navigating the challenges of swarm control required a strong foundation in ROS, evident in the creation of launch files and polymorphic scripts. However, the 4-wheel differential drive posed challenges in motion accuracy, suggesting potential improvements in robot configurations for future work.




---
layout: post
title:  "Complete Extended-Kalman-Filter SLAM Pipeline in C++ from Scratch"
categories: [ROS2/ROS, C++, Python, SLAM, Machine Learning, LIDAR, Wheeled Locomotion, Motion Planning, Controls, Data Structures, Turtlebot
]
image: assets/images/SLAM.gif
featured: false
hidden: false
---

ROS2/ROS, C++, SLAM, Machine Learning, Python, Wheeled Locomotion, Turtlebot

<br>

<div align="center">
<video width="90%" controls loop autoplay muted>
    <source src="https://github.com/ME495-Navigation/slam-project-GogiPuttar/assets/59332714/2776878f-69f1-487e-9ec8-3d6d252ae449
" type="video/mp4">
</video>
</div>

<br>

## Overview
The aim of this project is to developed a complete professionally documented and tested ROS2 C++ Extended Kalman Filter SLAM package  in ROS2 entirely from scratch for a Turtlebot3 Burger. 

These videos showcase my complete algorithm in action on an actual robot, and in simulation. In the bottom left corner, you'll find my most accurate estimations of the robot's position based on odometry alone (in blue) and enhanced by EKF SLAM (in green). 
The SLAM estimation notably outperforms odometry by providing a closer match to the real robot position, since it factors in elements like noise and wheel slippage which are not considered in odometry.
Moreover, the green cylinders indicate the positions of cardboard landmarks as detected by SLAM. 
The yellow landmarks represent interim outcomes of my circle fitting algorithm applied to lidar data, which is then integrated into SLAM updates. 
These measurements are then associated with known landmarks in the map constructed by SLAM, using the Mahalanobis distance.
Portions of the surrounding walls are identified as obstacles due to surface irregularities. 
All of the helper functions for these are defined in the `turtlelib` package.

<br>

<div align="center"><h2> <a href="https://github.com/ME495-Navigation/slam-project-GogiPuttar" target="_blank">View it on Github ⇗</a></h2></div>

<br>

## Simulation

<br>

<div align="center">
<video width="90%" controls loop autoplay muted>
    <source src="https://github.com/ME495-Navigation/slam-project-GogiPuttar/assets/59332714/b38505b0-d3cf-4aae-aefa-e1a778615cb1
" type="video/mp4">
</video>
</div>

<br>





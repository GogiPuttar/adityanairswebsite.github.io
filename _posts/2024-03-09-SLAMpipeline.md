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

## Low-Level Control
I've developed a C++ library designed to manage 2D transformation matrix operations along with the forward and inverse kinematics computations for a differential drive robot. This library aids in translating a desired robot velocity, typically communicated through the `cmd_vel` topic, into corresponding wheel commands. Additionally, it facilitates the conversion of updates in encoder readings into a revised robot position using odometry calculations.

<br>

## Localization and Mapping
I conducted online extended Kalman filter SLAM utilizing discrete features, which involves tracking the state of a robot in $$2$$-D (position coordinates $$x$$, $$y$$, and orientation $$\theta$$) along with $$n$$ map locations represented as cylinders centered at specific coordinates ($$m_{x_i}$$, $$m_{y_i}$$). 
More details for this can be found 
<a href="https://nu-msr.github.io/navigation_site/lectures/slam.pdf" target="_blank">here</a>.
The implementation entails making predictions of the robot's new state and covariance based on odometry estimates during each iteration. 
Subsequently, for every observed obstacle measurement, adjustments are made to refine the predictions of the map, robot state, and robot covariance. 
This approach consistently demonstrates superior performance compared to relying solely on odometry, proven through evaluations in both simulated environments and real-world scenarios.

<br>

## Perception Pipeline
First, I conducted supervised clustering of lidar data, which involves grouping together lidar points that are within a certain proximity threshold of each other.

Second, I proceeded with circle regression and circle classification on these clusters. This process involves determining 
<a href="https://projecteuclid.org/journals/electronic-journal-of-statistics/volume-3/issue-none/Error-analysis-for-circle-fitting-algorithms/10.1214/09-EJS419.full" target="_blank">the center and radius of a circle in 2D</a>, 
and evaluating whether it should be classified as a circle through radius filtering.

Finally, I performed data association, which entails deciding whether a detected circle should be identified as a new map object or associated with an existing obstacle. This is achieved by computing the Mahalanobis distance from the robot to each previously initialized obstacle and selecting the minimum distance. If this minimum distance exceeds a specified threshold, the circle is categorized as a new obstacle; otherwise, it is associated with the obstacle having the minimum Mahalanobis distance.

<br>

<div align="center">
<video width="90%" controls loop autoplay muted>
    <source src="https://github.com/ME495-Navigation/slam-project-GogiPuttar/assets/59332714/b38505b0-d3cf-4aae-aefa-e1a778615cb1
" type="video/mp4">
</video>
</div>

<br>

The above video shows my full SLAM algorithm working in simulation. Again, the blue robot represents the odometry estimate and the green robot/landmarks represent the SLAM estimates. The red robot and obstacles represent the simulated “true” locations. Over time, the SLAM robot sticks with the location of the simulated robot over the odometry robot.

<br>





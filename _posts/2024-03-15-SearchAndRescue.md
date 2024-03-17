---
layout: post
title:  "Autonomous Search and Rescue with UnitreeGo1"
categories: [ROS2/ROS, C++, Python, SLAM, Computer Vision, Legged Locomotion, Unitree, Zed, Exploration, Motion Planning, Dynamic Systems,Controls, Data Structures
]
image: assets/images/Dog.gif
featured: false
hidden: false
---

C++, ROS2/ROS, Python, SLAM, Computer Vision, Exploration, Legged Locomotion, Unitree, Zed

<br>

<div align="center">
<video width="80%" controls loop autoplay muted>
    <source src="https://github.com/ME495-Navigation/slam-project-GogiPuttar/assets/59332714/521cd789-6aac-4c77-86be-2921d3629d72" type="video/mp4">
</video>
</div>

<br>

## Overview
The aim of this project is to enable the Unitree Go1 to autonomously explore an outdoor environment with reasonably uneven terrain, in order to search and locate a missing person.
The motivation for this project is to allow robots to look for people missing in a natural environment ([example](https://www.youtube.com/watch?v=Fmxt7NZzmmU)), where gathering a search party ad-hoc is not very easy. 
This leverages the unique advantages that legged robots as well as visual SLAM possess, in unmapped environments. 
The dog will be equipped with a **ZED 2i camera** and use its built in [positional tracking](https://www.stereolabs.com/docs/positional-tracking) and mapping for SLAM, object detection be used for detecting human beings, some sort of **Frontier Exploration** will be used for search the environment while exploring, `nav2` will be used for navigating to these goal poses, and a **Central Pattern Generator (CPG)** to generate holonomic (gaze and heading are decoupled) walking motions on uneven terrain.
**This project is scheduled to be completed on 15th March 2024**.

<br>

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/DogTimeline.png?raw=true" width="100%"/>
<figcaption></figcaption>
</figure>

<br>

## System design

(flowchart)

### Unitree Go1

<br>

<div align="center">
<video width="80%" loop autoplay muted>
    <source src="https://github.com/ME495-EmbeddedSystems/Mapping-and-Exploration-in-ROS2/assets/59332714/407e18ca-219e-4e14-9dea-ff7d3f920b10" type="video/mp4">
</video>
</div>

<br>

### Jetson Orin Nano
- Isaac ROS
- Jetpack 5.1.2

### Zed camera
- Perfect, great documenttion, works very well
- ROS2 wrapper

<br>

## Visual SLAM

### Visual-Inertial Odometry

<br>

<p align = "center">
<video width="48%" loop autoplay muted>
    <source src="https://docs.stereolabs.com/positional-tracking/images/zed-positional-tracking.mp4" type="video/mp4">
</video>
<video width="48%" loop autoplay muted>
    <source src="https://github.com/GogiPuttar/adityanairswebsite.github.io/assets/59332714/ff766a79-617e-4516-a7f3-eaf4ef81644e" type="video/mp4">
</video>
</p>

<br>


### 3-D Map Building

<br>

<div align="center"><iframe width="972" height="750" src="https://www.youtube.com/embed/eCHhzRkATEU" title="Visual SLAM with Zed2i and UnitreeGo1" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe></div>

<br>

### Human Detection

<br>

### 2-D Map filtering

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/Dog3Dmap.png?raw=true" width="50%"/>
<figcaption>Fig. 1. Example trajectory of Catmull-Rom Splines</figcaption>
</figure>

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/Dog2Dmap.png?raw=true" width="50%"/>
<figcaption>Fig. 1. Example trajectory of Catmull-Rom Splines</figcaption>
</figure>

- Must be usable by Nav Stack
- Algorithm for free space generation
- Eroding away outliers
- RTAB map works better for lidar scans

<br>

## Nav Stack

- unitree_nav package by nick morales

<br>

<div align="center">
<video width="80%" controls loop muted>
    <source src="https://github.com/ME495-EmbeddedSystems/Mapping-and-Exploration-in-ROS2/assets/59332714/e8ebbd4d-7144-4a7c-b044-cb987d462da5" type="video/mp4">
</video>
</div>

<br>

## Frontier Exploration

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/DogFrontiers.png?raw=true" width="80%"/>
<figcaption>Fig. 1. Example trajectory of Catmull-Rom Splines</figcaption>
</figure>

(best exploration video)
 
- Considerations
    - Zed camera 30cm
    - Limited field of view
    - Holonomic

- BFS vs DFS for stereo camera

<br>






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
<p align="center">
<em>
Teleoperated concept video of the final product.
</em>
</p>

<br>

## Overview
The aim of this project is to enable the Unitree Go1 to autonomously explore an outdoor environment with uneven terrain, in order to search and locate a missing person.
The motivation for this project is to allow robots to look for people missing in a natural environment ([example](https://www.youtube.com/watch?v=Fmxt7NZzmmU)), where gathering a search party ad-hoc is difficult. 
This leverages the unique advantages that legged robots as well as visual SLAM possess, in unmapped environments. 
The dog (a **Unitree Go1**) is equipped with a **ZED 2i camera** and uses its built in Visual-Inertial Odometry (VIO) and mapping for SLAM, object detection for detecting human beings, a **frontier exploration algorithm** for searching and environment, and the **Nav Stack** for traveling to goal poses.
Much of the work in this project has been about integrating many different sensors and computers using ROS2 `humble` on an **Nvidia Jetson Orin Nano**.

Given below is a road map for completing the project.
Currently, I'm at a point where the exploration algorithm is only suitable for dense environments, and hence has not been deployed for large and sparse outdoor environments:

<br>

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/DogTimeline.png?raw=true" width="100%"/>
<figcaption></figcaption>
</figure>

<br>

<div align="center"><h2> <a href="https://github.com/GogiPuttar/Search_for_missing_person_Unitree_Go1/tree/main" target="_blank">View it on Github ⇗</a></h2></div>

<br>

## System design

The system consists of a Unitree Go1, a Zed 2i Camera and an Nvidia Jetson Orin Nano, and uses ROS2 `humble`. 
The Unitree and Jetson are connected using an ethernet cable while the Zed is connected to the Jetson through USB.
All of these components are fixed to the Unitree using a 3-D printed mount designed by [David Dorf](https://www.daviddorf.com/). 

<br>

<figure align = "center">
<img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/DogFront.jpg?raw=true" width="45%"/>
<img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/DogSide.jpg?raw=true" width="45%"/>
<figcaption><em>Fig. 1. Assembled hardware. A buck converter (24V->12V) from the battery port (XT30) to the Jetson is also required.
</em></figcaption>
</figure>

<br>

All ROS2 nodes run on the Jetson, and can be activated and visualized using an external computer through `ssh -Y` over WiFi, using the same ROS2 distro (`humble` in my case).
Wireless communication with ROS2 can be quite unreliable however, and a wired connection is recommended whenever possible, *especially* for testing. 

<br>

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/DogDiagram.png?raw=true" width="80%"/>
<figcaption><em>Fig. 2. A block diagram of all the active elements in the system.</em></figcaption>
</figure>

<br>

### Unitree Go1

The Unitree Go1 serves as a robust, holonomic mobile base that can easily be deployed on moderately uneven terrain. 
The [Unitree ROS2 wrapper](https://github.com/katie-hughes/unitree_ros2) by [Katie Hughes](https://katie-hughes.github.io/) has been utilized for high level control, and communication between the two needs to be initialized every time using:

```
ifconfig # Tells you enpxxx, your computer's network interfaces 
sudo ifconfig enpxxx down
sudo ifconfig enpxxx 192.168.123.162/24
sudo ifconfig enpxxx up
ping 192.168.123.161 
```
I would recommend turning these commands into an alias and running it each time the Unitree and Jetson are switched on.

<br>

<div align="center">
<video width="80%" loop autoplay muted>
    <source src="https://github.com/ME495-EmbeddedSystems/Mapping-and-Exploration-in-ROS2/assets/59332714/407e18ca-219e-4e14-9dea-ff7d3f920b10" type="video/mp4">
</video>
</div>
<p align="center">
<em>
High Level Control on the Unitree Go1.
</em>
</p>

<br>

### Jetson Orin Nano
The Jetson development boards by NVidia are compact, reliable and powerful Linux computers that can easily serve as the brain of various robotic systems.
The Jetson Orin Nano has been flashed using a micro SD card with the [Jetpack 5.1.2](https://developer.nvidia.com/embedded/jetpack-sdk-512), and runs Ubuntu 20.0.4 and ROS2 `humble`. 
ROS2 can be installed by following this [Isaac ROS](https://nvidia-isaac-ros.github.io/getting_started/isaac_ros_buildfarm_cdn.html) tutorial.
[Shail Dalal](https://sdalal1.github.io/projects/) and I tried multiple ways to flash the [Jetpack 6.0](https://developer.nvidia.com/embedded/jetpack) onto the Jetson Orin Nano (with Ubuntu 22.0.4 and ROS2 `iron`) but were unsuccessful, so we opted to use the Jetpack 5.1.2 instead. 
Setting up a Jetson from scratch and installing packages on it can quickly become a tiring process owing to the lack of software support and documentation by Nvidia, which must be anticipated beforehand.
After being set up however, the Jetson should operate quite reliably.

<br>

### Zed 2i camera
The Zed series of cameras by StereoLabs are a highly accurate stereo cameras which have built in Visual-Inertial Odometry, 3-D Mapping, and Object (including human) Detection, among other features.
Additionally, the cameras have a very well documented [ROS2 wrapper](https://github.com/stereolabs/zed-ros2-wrapper) making rapid deployment of these features possible. 

<br>

## Visual SLAM

The Zed camera's superior localization and mapping capabilities can be used to get Visual SLAM straight out of the box.
Multiple parameters and flags in the `config/common.yaml` file of the wrapper can be adjusted to get many customized behaviours:

<br>

### Visual-Inertial Odometry

This feature is highly accurate and does not accumulate drift. 
However, since the depth perception of the camera does not work from 0-40cm, objects traveling in and out of this range can seriously disorient the camera's perception.
The odometry is also robust to the vibrations caused by the Unitree walking.

<br>

<p align = "center">
<video width="48%" loop autoplay muted>
    <source src="https://docs.stereolabs.com/positional-tracking/images/zed-positional-tracking.mp4" type="video/mp4">
</video>
<video width="48%" loop autoplay muted>
    <source src="https://github.com/GogiPuttar/adityanairswebsite.github.io/assets/59332714/ff766a79-617e-4516-a7f3-eaf4ef81644e" type="video/mp4">
</video>

<em>
On the left is 
<a href="https://nu-msr.github.io/hackathon/wavefront.html" target="_blank">Stereo Lab's demonstration</a>, and on the right is my test of the Zed camera's positional tracking.
</em>
</p>

<br>

### 3-D Map Building

The 3-D map building tool is also very powerful and does not accumulate noticeable drift over large distances.
However, it can register false locations because of virtual objects in reflective surfaces.
It is also not robust to dynamic obstacles which is addressed in the [map filter](https://adityanairs.website/SearchAndRescue/#:~:text=and%20rescue%20operation.-,2%2DD%20Map%20filtering,-The%203D%20map).

Here's a video of the 3-D map building integrated with the 2-D filtered map, while manually operating the Unitree:

<div align="center"><iframe width="672" height="660" src="https://www.youtube.com/embed/eCHhzRkATEU" title="Visual SLAM with Zed2i and UnitreeGo1" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe></div>

<br>

### Human Detection

Human detection is part of the object detection feature and can even [track the motion of crowds of people](https://katie-hughes.github.io/crowdnav/). 
This is used to trigger a behaviour change upon encountering a human during the search and rescue operation.

<br>

### 2-D Map filtering

The 3D map needs to be projected onto a 2D grid to be utilized by Nav2. 
While [RTAB Map](https://introlab.github.io/rtabmap/) is the most popular tool to project 3-D point clouds from LIDAR scans onto 2-D occupancy grids, the unique naunces associated with the Zed camera's 3-D mapping (reflective surfaces, dynamic obstacles etc.) warrant a fair amount of post-processing making it more sensible to implement a custom node for this. 
For example, a human walking in front of the camera can register enough outliers to create the illusion of various pseudo-obstacles which can seriously bias the Nav2 costmap.

<br>

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/Dog3Dmap.png?raw=true" width="80%"/>
<figcaption>Fig. 3. Example of how dynamic obstacles can result in outliers in the well formed 3-D map.</figcaption>
</figure>

<br>

The algorithm I have implemeneted for this is:

1. Iterate over the point cloud and increment the cells of a 2-D occupancy grid for each point lying within a range of height values above the floor.

    1. If the number of points in a grid cell is above a certain threshold, classify it as an obstacle.
    2. Otherwise, if there are enough point close to and below the floor, or there are enough points close to the ceiling, classify it as a free cell.
    3. Otherwise, classify it as an unexplored cell.

2. Erode the map a few times in order to get rid of outliers. False obstacles are more harmful to the cost map than false free spaces.

3. Pad the boundaries of the map with obstacles for safety.

4. Clear the initial heading of the Unitree assuming that it will be deployed facing no immediate obstacles.

This algorithm is robust to smaller dynamic obstacles but not large ones.

<br>

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/Dog2Dmap.png?raw=true" width="80%"/>
<figcaption><em>Fig. 4. A processed 2-D map suitable for Nav2.</em></figcaption>
</figure>

<br>

## Nav Stack

I have used the `unitree_nav` [package](https://github.com/ngmor/unitree_nav) by [Nick Morales](https://ngmor.github.io/), [Marno Nel](https://marnonel6.github.io/), and [Katie Hughes](https://katie-hughes.github.io/). 
This hosts a convenient service which allows setting and updating of `nav2` goal poses.
Since this package is integrated with a RoboSense LIDAR, custom launch files have been made to integrate the Zed camera instead.

<br>

<div align="center">
<video width="80%" controls loop muted>
    <source src="https://github.com/ME495-EmbeddedSystems/Mapping-and-Exploration-in-ROS2/assets/59332714/e8ebbd4d-7144-4a7c-b044-cb987d462da5" type="video/mp4">
</video>
</div>
<p align="center">
<em>
Unitree Go1 fully integrated with the Nav Stack.
</em>
</p>

<br>

## Frontier Exploration

There are certain salient considerations that were kept in mind while writing the frontier exploration algorithm.

- The Zed camera's depth resolution does not work for distances smaller than 30cm. The position of the `base_footprint` must be adjusted to not let the zed camera be too close to an obstacle while traveling to or reaching a frontier.
- A Stereo Camera has a limited viewing angle. When initialized in a new environment, any frontiers behind the camera will immediately be the closest to travel to and might not lead to desired behaviour.
- The Unitree is a holonomic mobile base and therefore has decoupled view and heading. If used correctly, this can be a major advantage in efficiently exploraing an area. 
- The map may have small encalves of unexplored regions which can be outliers for the exploration algorithm.

My frontier exploration algorithm works as follows:

1. Dilate the free space of the map to cover any enclaves.
2. Locate the position and direction of each frontier by evaluating neighboring unexplored cells around free cells.
3. Update the goal pose by picking the closest frontier at every time step. This means that traveling exactly to a particular frontier is less of a priority than constantly moving and exploring.

<br>

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/DogFrontiers.png?raw=true" width="80%"/>
<figcaption>
<em>
Fig. 5. Frontiers are identified and the closest frontier is chosen as the goal pose.
</em>
</figcaption>
</figure>

<br>

### Deploying the Algorithm Outdoors

<br>

<div align="center">
<video width="80%" controls loop muted>
    <source src="https://github.com/ME495-EmbeddedSystems/Mapping-and-Exploration-in-ROS2/assets/59332714/665011b5-b424-4f12-9bd0-36ba38541acc" type="video/mp4">
</video>
</div>
<p align="center">
<em>
Frontier exploration successfully working in a tight space.
</em>
</p>

<br>

While the exploration worked in a smaller indoor environment, it wasn't suitable to be deployed in a sparse outdoor enviornment. 
This is because the current exploration algorithm picks the closest reachable frontier which is more explorative and less exploitative akin to a Breadth First Search.
This leads to the robot spending a large time exploring its immediate surroundings rather than commiting to a path and covering larger ground.
On the other hand, a highly exploitative algorithm akin to Depth First Search which is biased to covering larger ground in less time is not guaranteed to explore the space properly which might lead to swathes of unexplored regions.

<br>

## Acknowledments

<!-- <figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/DogFrontiers.png?raw=true" width="80%"/>
<figcaption>
<em>
Fig. 5. Frontiers are identified and the closest frontier is chosen as the goal pose.
</em>
</figcaption>
</figure> -->


Thanks, Stella for starring in my concept video.







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

<div align="center"><iframe width="972" height="750" src="https://www.youtube.com/embed/eCHhzRkATEU" title="Visual SLAM with Zed2i and UnitreeGo1" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe></div>

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




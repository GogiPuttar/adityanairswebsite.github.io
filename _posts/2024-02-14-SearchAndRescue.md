---
layout: post
title:  "Autonomous Search and Rescue with UnitreeGo1 (in progress)"
categories: [ROS2/ROS, C++, Python, SLAM, Computer Vision, Legged Locomotion, Unitree, Zed, Exploration, Motion Planning, Dynamic Systems,Controls, Data Structures
]
image: assets/images/UnitreeMapping.gif
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

## Technical Objectives

### Fallback Goal:
To make the robot to search for any human being in an indoor environment. This principally
negates the anticipated problems of: walking on uneven terrain, bad performance of the ZED
camera outdoors and perhaps even having all the computation on-board. If for some reason the
frontier exploration algorithm also does not work, modeling the indoor environment as a known
environment will also be simpler. This still retains the tag of hide-and-seek but might not be
search and rescue anymore.

### Core Goal:
To make the robot search for a human being sitting behind a tree in relatively small environment
with a lot of trees, within a reasonable amount of time (5 min in the smallest feasible
environment and 30 min in an impressively large environment).

### Stretch Goal:
To make the robot confirm someone’s identity by facial recognition, so it only looks for specific
people and does not finish its operation after finding just anyone. This will require some facial
recognition model as well as adjusting the exploration algorithm to better inspect a human target
by moving closer to them.

OR

To make the robot identify human objects as clues while performing its search. Cataloging clues
of missing persons is a widely open sourced venture. Even if the robot is unsuccessful in finding
the missing person, getting information like the location and photo of the hairband they were
known to be wearing before getting lost can be a valuable segue into human intervention.

<br>

## Learning Objectives:
Since this project requires so many systems to run reliably and in parallel, over a significant
period of time for successful operation, the main challenge is integration. I really need to know
my ROS2 to accomplish this.

I intend to use a holonomic controller for the robot which would make the gaze and heading
independent. I personally feel that this is an important step which should be thought about in the
early stages, since this allows us to fully leverage all of the advantages that a legged robot
would possess. No more assuming the unitree to be diff-drive.

Navigation is also a crucial part of this project which I am new to, and I will get to learn a lot
about it.

<br>



---
layout: post
title:  "Data-Driven Control for Agile Flight in a Confined Space"
categories: [C++, Python, ROS2/ROS, Motion Planning, Dynamic Systems, Controls, Data Structures, Aerial Robots, Mechatronics, Circuit Design, Computer Vision, CMake
]
image: assets/images/Bird2.gif
featured: false
hidden: false
---
C++, Python, ROS2/ROS, Aerial Robots, Motion Planning, Computer Vision, Dynamic Systems 

<br>

<div align="center"><iframe width="1333" height="650" src="https://www.youtube.com/embed/oVQGfwPvMw4" title="Data Driven Control of an Agile Robot Bird in a Confined Space" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe></div>

<br>

## Overview

<br>

<figure align = "center">
<img src="{{ site.baseurl }}/assets/images/birdsideview.gif" width="80%"/>

<img src="{{ site.baseurl }}/assets/images/birdtopview.gif" width="80%"/>
<figcaption><em> Top and side views of the bird successfully flying in circles.
</em></figcaption>
</figure>

<br>

<div align="center"><h2> <a href="https://github.com/GogiPuttar/Search_for_missing_person_Unitree_Go1/tree/main" target="_blank">View it on Github ⇗</a></h2></div>

<br>

## Personal Motivation

### MetaFly by Bionic Bird

<br>

<div align="center"><iframe width="650" height="317" src="https://player.vimeo.com/video/377080293?autoplay=1&amp;dnt=1&app_id=122963" title="Data Driven Control of an Agile Robot Bird in a Confined Space" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe></div>
<p align="center">
<em>
MetaFly product video.
</em>
</p>

<br>

### How hard is it to fly this thing?

<br>

## Control Architecture

<figure align = "center"><img src="{{ site.baseurl }}/assets/images/birdcontrolsgraph.svg" width="95%"/>
<figcaption>
<em>
Fig. 1. Final control architecture of the system
</em>
</figcaption>
</figure>

<br>

## Motion Planner

<div align="center">
<video width="90%" controls loop autoplay muted>
    <source src="https://github.com/user-attachments/assets/7d3f5dd1-43ff-4e1a-bd0b-074c8641f635" type="video/mp4">
</video>
</div>
<p align="center">
<em>
The motion planner in action viewed through RViz.
</em>
</p>

<br>

## System Design

<figure align = "center"><img src="{{ site.baseurl }}/assets/images/birdsystem2.svg" width="95%"/>
<figcaption>
<em>
Fig. 2. The whole system as a control loop
</em>
</figcaption>
</figure>

### Transmitter - Feedforward

<figure align = "center">
<img src="{{ site.baseurl }}/assets/images/birdremote.jpg" width="51.7%"/>
<img src="{{ site.baseurl }}/assets/images/birdremoteschematic3.svg" width="45%"/>
<figcaption><em>Fig. 3. Schematic and picture of the electronically controlled transmitter module for the MetaFly
</em></figcaption>
</figure>

### Motion Capture - Feedback

<figure align = "center"><img src="{{ site.baseurl }}/assets/images/mocap.gif" width="90%"/>
<figcaption><em>The bird being tracked by multiple cameras of the OptiTrack system.
</em></figcaption>
</figure>

<iframe src="{{ site.baseurl }}/assets/images/3d_plot_interactive_spin.html" width="100%" height="500px"></iframe>
<p align="center">
<em>
Fig. 4. The portion of the drone cage that is observable using the OptiTrack system.
</em>
</p>

<br>

## Experiments

### Screw Trajectory

<div align="center">
<video width="80%" controls loop autoplay muted>
    <source src="https://github.com/user-attachments/assets/46f692a1-a44e-469a-bf53-0ba79c8d96f4" type="video/mp4">
</video>
</div>
<p align="center">
<em>
Screw-like trajectory of the robot. The green dot represents the instantaneous center on the x-y plane.
</em>
</p>

### Data Collection

<figure align = "center"><img src="{{ site.baseurl }}/assets/images/birdpitchvsteering.png" width="60%"/>
<figcaption><em>Fig. 5. Relation between screw pitch and steering command for 100+ trials at constant max speed input.
</em></figcaption>
</figure>

<figure align = "center"><img src="{{ site.baseurl }}/assets/images/birdrollvsteering.png" width="60%"/>
<figcaption><em>Fig. 6. Relation between roll and steering command for 100+ trials at constant max speed input.
</em></figcaption>
</figure>

<br>

## Future Work

### 1. Reactive Drift Control

<figure align = "center"><img src="{{ site.baseurl }}/assets/images/birddrift.gif" width="60%"/>
<figcaption><em>The bird drifting and the instantaneous measured drift.
</em></figcaption>
</figure>

### 2. Imitation Learning through Data Collection

<figure align = "center"><img src="{{ site.baseurl }}/assets/images/birdps3.jpg" width="50%"/>
<figcaption><em>Fig. 7. The bird can even be controlled using a PS3 remote through ROS2.
</em></figcaption>
</figure>

### 3. Tuning gains "on the fly"

### 4. Transitioning from one set of constraints to another

### 5. Minimal radius control 

<br>

## Acknowledgements

<figure align = "left"><img src="{{ site.baseurl }}/assets/images/jakeketchum.jpg?raw=true" width="20%"/>
</figure>

***Thanks <a href="https://jake-ketchum.squarespace.com/" target="_blank">Jake</a>, for mentoring me throughout this project, and for being so nice. You're someone I can really talk to :)***

<figure align = "left"><img src="{{ site.baseurl }}/assets/images/davin.jpg?raw=true" width="20%"/>
</figure>

***Thanks <a href="https://dlandry97.github.io/Davin_Landry/" target="_blank">Davin</a>, for helping me build a totally epic DRONE CAGE!!***

<figure align = "left"><img src="{{ site.baseurl }}/assets/images/drew-curtis.png?raw=true" width="20%"/>
</figure>

***Thanks <a href="https://robotics.northwestern.edu/people/profiles/students/curtis-drew.html" target="_blank">Drew</a>, for helping me set up and debug the OptiTrack system.***

<br>





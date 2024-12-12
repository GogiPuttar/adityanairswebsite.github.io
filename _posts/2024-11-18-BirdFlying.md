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

<div align="center"><h2> <a href="https://github.com/GogiPuttar/ros2_metafly" target="_blank">View it on Github ⇗</a></h2></div>

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

The control architecture was developed after several hours of experimentation, involving tradeoffs between efficacy and complexity.
At the heart of it is an altitude ($$z$$) controller that commands speed ($$\sigma$$), and a roll ($$\alpha$$) controller that outputs steering ($$\phi$$).
This assumes that the bird is two parallel Single Input Single Output (SISO) system as opposed to a Multi Input Multi Output (MIMO) system, which is fair since the height and roll are the only known parameters that remain relatively independent, at least in the controls domain that we prefer to operate the bird in.
More on the domain in [the Data Collection section](#data-collection).
The controller is exactly described by:

$$
\begin{equation}
    \sigma = \sigma_{d}~+~K_{p_z}(z_t - z),
\end{equation}
$$

$$
\begin{equation}
    \phi = K_{p_{\alpha}}(\alpha_t - \alpha)~+~K_{d_{\alpha}}(\dot{\alpha}_t - \dot{\alpha}),
\end{equation}
$$

where $$\sigma_d$$ is the feedforward speed command required to maintain altitude. 
Here, the desired roll ($$\alpha_t$$) is either:

$$
\begin{equation}
    \alpha_t = \alpha_d + K_{p_{R}}(R_t - R),
\end{equation}
$$

or 

$$
\begin{equation}
    \alpha_t = \alpha_d + K_{p_{\gamma}}(\gamma_t - \gamma),
\end{equation}
$$

decided by the motion planner, which also calculates the desired yaw ($$\gamma_t$$) and the desired radius ($$R_t$$).
More on this in [the Motion Planner section](#motion-planner).
The tunable parameters of this architecture are: $$\{K_{p_z}, K_{p_\alpha}, K_{p_R}, K_{p_\gamma}, K_{d_\alpha}, \sigma_{d}, \alpha_d\}$$, which are distributed across controllers making them relatively easy to tune.
There are more tunable parameters in the motion planner.

<figure align = "center"><img src="{{ site.baseurl }}/assets/images/birdcontrolsgraph2.svg" width="95%"/>
<figcaption>
<em>
Fig. 1. Final control architecture of the system
</em>
</figcaption>
</figure>

<br>

## Motion Planner

The goal of the motion planner is to counteract the overall drift of the system that accumulates over time, to prevent the bird from violating the constraints of the room. 
This switches the behaviour over longer time horizons, since experiments with reactive approaches did not give good results.
This also relies on a special elliptical manifold which functions as mixture of the target circle and cuboidal constraints of the drone cage.
This aproach does not have a strong theoretical motivation, although a blending objective-based controller that emulates the functionality of this motion planner might make it a less contrived approach which could be used in other systems.
The tunable parameters for this are:
- The center and dimensions of the ellipse
- The range about the tangent for the heading vector

The pseudocode for the motion planner is:

```
START

DEPLOY a height controller for speed command

IF the bird is inside the ellipse:
  DEPLOY a radius controller for steering command, to track the target circle's radius

OTHERWISE:
  IF the tangent vector from the bird to the target circle is within a certain range of the bird's heading vector:
    DEPLOY a yaw controller to make the bird travel along the tangent

  OTHERWISE:
    DEPLOY a radius controller to make a sharp turn, so that it doesn't stray too far from the ellipse

GO TO START
```

Here's a video of the motion planner in action.
<br>
<div align="center">
<video width="90%" controls loop autoplay muted>
    <source src="https://github.com/user-attachments/assets/7d3f5dd1-43ff-4e1a-bd0b-074c8641f635" type="video/mp4">
</video>
</div>
<p align="center">
<em>
The motion planner in action, viewed through RViz. The bounding box is green when the bird is inside the ellipse, and mauve when the bird is outside it.
</em>
</p>
<br>

## System Design

The system comprises: ROS2 Network on a Linux Device, transmitter module for MetaFly, Windows machine with the OptiTrack, and the MetaFly itself, which form a closed loop together:
<br>
<figure align = "center"><img src="{{ site.baseurl }}/assets/images/birdsystem2.svg" width="95%"/>
<figcaption>
<em>
Fig. 2. The whole system as a control loop
</em>
</figcaption>
</figure>

<br>

### Transmitter - Feedforward
The transmitter module consists of an **Arduino Nano**, the **MetaFly remote controller's PCB** with it's potentiometers remote and a small circuit which all fits together as interchangeable shields.
It's job is to take speed and steering commands through serial communication and transmit them to the bird by emualting potentiometers using the Arduino's analog output and a capacitor.
I picked a Nano over a Teensy or something else of higher quality because it's cheap and easy to replace.

<figure align = "center">
<img src="{{ site.baseurl }}/assets/images/birdremote.jpg" width="51.7%"/>
<img src="{{ site.baseurl }}/assets/images/birdremoteschematic3.svg" width="45%"/>
<figcaption><em>Fig. 3. Schematic and picture of the electronically controlled transmitter module for the MetaFly
</em></figcaption>
</figure>

### Motion Capture - Feedback

The OptiTrack system is incredibly useful for aerial vehicles with a small payloads, but because the MetaFly weighs ~10g and has barely any payload, I had to create my own retroreflective markers using styrofoam balls and retroreflective tape.
These weighed 1/5th of similar-sized standard motion capture markers (~0.2g vs ~1g). 
After attaching these to the bird in whatever manner you want, you can register your bird as a rigid body by placing it at the origin of the motion capture system with the bird aligned with the X-axis.
If you require, the ROS2 listener node can also add a fixed transform offset to the bird's pose using the [`pose_offsets.yaml`](https://github.com/GogiPuttar/ros2_metafly/blob/main/metafly_listener/config/pose_offsets.yaml) file.
I also built a drone cage around the OptiTrack system using ropes and batting nets.

<figure align = "center"><img src="{{ site.baseurl }}/assets/images/mocap.gif" width="90%"/>
<figcaption><em>The bird being tracked by multiple cameras of the OptiTrack system.
</em></figcaption>
</figure>

A python script on the windows machine sends UDP pose messages over an ethernet connection to the ROS2 listener node. 
Set the Motive software to broadcast with **loopback** and select only the rigid body you want to broadcast.
Then run the broadcast script which should keep spitting out some output.
Connect your Linux device via ethernet to the Windows machine and run this command on the Linux device to configure it's settings, after which the `metafly_listener` node should publish the correct pose values on the `/metafly_pose` topic:

```
sudo ifconfig eno0 192.168.1.2 netmask 255.255.255.0 up
```

The observable workspace with the initial motion capture setup involving 10 cameras did not have great coverage over drone cage's volume.
I added on and refocused 6 more cameras to get almost full coverage.
This is what the observable workspace looks like now:

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

The controls domain comprises integer values for speed ($$0~\mbox{-}~127$$) and steering ($$-127~\mbox{-}~127$$), and the range we prefer to operate 
 ($$\sigma \approx 0~\mbox{-}~127$$, $$\phi \approx -70~\mbox{-}~70$$).

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





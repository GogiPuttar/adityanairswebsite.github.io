---
layout: post
title:  "Dancing Hexapod - Full SE3 Body Pose Control"
categories: [Python, Controls, Motion Planning, Simulation, Legged Locomotion, PyBullet, HEBI Daisy Hexapod, Data Structures

]
image: assets/images/AndiMandiShandi.gif
featured: true
hidden: true
---

Python, Motion Planning, Legged Locomotion, PyBullet, HEBI Daisy Hexapod

<br>

<div align="center">
<video width="75%" controls loop autoplay muted>
    <source src="https://github.com/GogiPuttar/adityanairswebsite.github.io/assets/59332714/f81ca225-8e2e-4902-b735-e2e5997a4b14" type="video/mp4">
</video>
</div>
<div align="center">
<em><b>To feel the rhythm of the robot, I recommend you turn your volume on! 🔊</b></em>
</div>

<br>

## Overview

I created a framework for full $$SE(3)$$ body pose control for HEBI's Daisy Hexapod, and made it dance using it's front legs and body pose to the [Crab Rave](https://www.youtube.com/watch?v=LDU_Txk06tM){:target="_blank" rel="noopener"} song. 
This robot is simulated in PyBullet, and follows a sequence of 17 keyframes in order to complete its dancing motion.
These keyframes are also stitched together using a Catmull-Rom spline (described below) in order to make the motion appear more natural and rhythmic, which really brings this to life.

<br>

<div align="center"><h2> <a href="https://github.com/marmotlab/Yuna_PyBullet/blob/main/setup/yunaAnimatronics.py" target="_blank">View it on Github ⇗</a></h2></div>

<br>

## Personal Motivation

While writing my bachelor's thesis at the [MARMot Lab](https://marmotlab.org/){:target="_blank" rel="noopener"}, National University of Singapore, I derived the inverse kinematics and velocity kinematics for the body pose of a six-legged robot, to gain insight on the load distributiion over its joints. 
One fine day, I happened to come across [Boston Dynamics' Video of Spot and Atlas dancing](https://www.youtube.com/watch?v=fn3KWM1kuAw){:target="_blank" rel="noopener"} and thought that this was the coolest thing ever. 
I tried to see if I could do something similar with MARMot's hexapod, but I couldn't figure how exact I would make something dance, whose body plan is similar to an insect. 
This is when I had the AHA! moment as I recalled the [Crab Rave](https://www.youtube.com/watch?v=LDU_Txk06tM){:target="_blank" rel="noopener"} meme, and then spent the next couple of days making this work.
What I ended up with was a flawless demonstration of my body pose controller.
Suffice to say, the Lab loved it `:)`

<br> 

## Full $$SE(3)$$ Body Pose Control

Provided sufficient forces, the standing legs of a legged robot can "manipulate" the ground in its body's frame, to any pose within a reachable workspace; thereby allowing body pose control in the ground frame. 

This is my ***complete derivation*** of Body Pose Control in full $$SE(3)$$. 
This can be applied to any standing legged robot / delta platform provided it has sufficient degrees of freedom and reliable IK.
In this case, the $$3$$ standing legs possess $$9$$ degrees of freedom which is greater than $$6$$ required for body pose control (kinematically redundant).
I have also derived an equivalent expression for ***velocity kinematics*** and a ***$$9 \times 6 $$ Body Pose Jacobian*** but I have excluded it here to save space since it was not directly utilized for this project.

Given the world frame $$a$$, the initial body frame (home configuration) $$b$$ and the changed body frame $$c$$. We define the body pose $$[x, y, z, \alpha, \beta. \gamma]^T$$ such that:

$$
T_{bc} = 
\begin{bmatrix}
    &  &  & x\\
    & R_z(\gamma)R_y(\beta)R_x(\alpha)& & y\\
    &  &  & z\\
   0 & 0 & 0 & 1
\end{bmatrix},
$$

in the standard order, roll ($$\alpha$$) $$\rightarrow$$ pitch ($$\beta$$) $$\rightarrow$$ yaw ($$\gamma$$) in local frames.

Further, the positions of the three standing feet $$p_i: i = \{1,2,3\}$$, in the instantaneous body frame, are what we command to the IK-based controller to change the body pose. 
These feet positions are static in the world frame $$a$$. 
Therefore, changing these achieve a certain body pose amounts to only a simple reference frame shift of these points:

$$
p_{i,C} = T_{bc}^{-1} p_{i,B} : i = \{1, 2, 3\}.
$$

<br>

## Animatronic Motion

Trajectory planning for the robot's body and legs in order to make the robot dance cannot be as simple as iteratively commanding target poses to the robot.
In order to make the motion appear organic, I decided to stitch these various target poses in the task space, using the simple Catmull-Rom spline. 
Here, the task space is defined by the $$3+3+6=12$$-dimensional space in which each target pose of the robot lies.

<br>

<div align="center">
<video width="65%" controls loop autoplay muted>
    <source src="https://github.com/GogiPuttar/adityanairswebsite.github.io/assets/59332714/7f364020-b06b-46a9-ac32-d0b2ca041a63" type="video/mp4">
</video>
</div>

<br>

### Catmull-Rom Splines

The Catmull-Rom Spline, originally formulated by Edwin Catmull (Co-Founder of Pixar) and Raphael Rom, is a smooth curve that can be made to pass through certain $$n$$-dimensional points.

The advantages this specific spline possesses over other widely used curves are:

1. Unlike **Bezier Curves**, Catmull-Rom splines actually pass through every point, and additionally do not increase in polynomial order with increasing target points.

2. Unlike **NURBS**, these splines do not have a complicated iterative formula.

3. Unlike **general Splines**, the points along the original set of points can also make up the control points for this curve.

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/SplineTrajectory.png?raw=true" width="50%"/>
<figcaption>Fig. 1. Example trajectory of Catmull-Rom Splines</figcaption>
</figure>

To create such a spline we need to extra control points, before the first point and after the last point in the curve.
Within a loop, these could also just be the last and first points respectively.
The parameterization is described as follows:

$$
q(s) = 
\begin{bmatrix}
    1 & u & u^2 & u^3\\ 
    \end{bmatrix} 

\begin{bmatrix}
    0 & 1 & 0 & 0\\
    -\tau & 0 & \tau & 0\\
    2\tau & \tau-3 & 3 - 3\tau & -\tau \\
    -\tau & 2 - \tau & \tau - 2 & \tau \\ 
    \end{bmatrix}

\begin{bmatrix}
    q_{i-2}\\
    q_{i-1}\\
    q_{i}\\
    q_{i+1}\\ 
    \end{bmatrix},
$$

where $$q_i$$ is the $$i$$-th target configuration.

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/SplineBlendingFunction.png?raw=true" width="50%"/>
<figcaption>Fig 2. Blending functions for Tau = 2</figcaption>
</figure>

## Conclusion
While it is self-evident that the simulation behaves how it is supposed to, I was unsuccessful in the completing my final goal of taking our real hexapod to the Marina Bay Sands beach in Singapore and filming it dancing over there. 
This is primarily because even though all of these target poses are within the reachable workspaces, given the 20kg weight of the actual robot, it cannot statically stand in some of these configurations. Given the somewhat chaotic nature of the dynamics of such a complex system, surfing the edge of what the robot can and cannot support is a precarious task. 
Maybe someday when I have the time, I'll be able to actualize this dream too.

## References

1. [Noisestorm's Crab Rave](https://www.youtube.com/watch?v=LDU_Txk06tM){:target="_blank" rel="noopener"}
2. [Christopher Twigg on Catmull-Rom Splines](https://www.cs.cmu.edu/~fp/courses/graphics/asst5/catmullRom.pdf){:target="_blank" rel="noopener"}
3. [javidx9's Series on Using Splines](https://www.youtube.com/watch?v=9_aJGUTePYo&t=1127s){:target="_blank" rel="noopener"}




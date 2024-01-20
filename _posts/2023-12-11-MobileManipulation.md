---
layout: post
title:  "Mobile/Whole-Body Manipulation with KUKA youBot"
categories: [MATLAB, Manipulation, Motion Planning, Controls, Simulation, Wheeled Locomotion, CoppeliaSim, KUKA youBot, Data Structures

]
image: assets/images/MobileManipulation.gif
featured: false
hidden: false
---

MATLAB, Manipulation, Motion Planning, Controls, CoppeliaSim, KUKA youBot

<br>

<div align="center">
<video width="80%" controls loop autoplay muted>
    <source src="https://github.com/GogiPuttar/adityanairswebsite.github.io/assets/59332714/a663013e-a913-4e6c-8d2e-881ea00691ce" type="video/mp4">
</video>
</div>

<br>

## Overview
This was the Capstone Project for the course: ***ME 449 Robotic Manipulation*** by ***Dr. Kevin Lynch*** at Northwestern University.
In this project, I wrote software that ***plans a trajectory*** for the end-effector of a KUKA youBot mobile manipulator (a mobile base with four mecanum wheels and a 5R robot arm), ***performs odometry*** as the chassis moves, and performs ***feedback control*** to drive the youBot to pick up a block at a specified location, carry it to a desired location, and put it down.
Functions from the `modern-robotics` library were used in this project. 

The final output of my software is a comma-separated value (csv) text file generated using a MATLAB script that specifies the configurations of the chassis and the arm, the angles of the four wheels, and the state of the gripper (open or closed) as a function of time. 
This specification of the position-controlled youBot was then tested on the CoppeliaSim simulator.
Here, the ODE physics engine was used to simulate contact dynamics and inertial forces.

**The program takes as input:**

- The initial resting configuration of the cube object (which has a known geometry), represented by a frame attached to the center of the object
- The desired final resting configuration of the cube object
- The actual initial configuration of the youBot.
- The initial configuration $$T_{se}$$ of the reference trajectory for the end-effector frame of the youBot 

**The program's output is:**

- A csv file which, when "played" through the CoppeliaSim scene, drives the youBot to successfully pick up the block and put it down at the desired location.
- A data file containing the 6-vector end-effector error as a function of time

<br>

<!-- ## Personal Motivation -->



## Kinematics of the KUKA youBot

<br>

<figure align = "center">
    <img src="https://hades.mech.northwestern.edu/images/thumb/3/33/Yb-book.png/1200px-Yb-book.png" width="65%"/>
    <figcaption><em>Fig. 1. This figure illustrates the arm at its home configuration (all joint angles zero) and the frames {s}, {b}, {0}, and {e}. For the image on the right, joint axes 1 and 5 (not shown) point upward and joint axes 2, 3, and 4 are out of the screen. 
    <a href="https://hades.mech.northwestern.edu/index.php/File:Yb-book.png" target="_blank">[Image Source]</a></em></figcaption>
</figure>

<br>

The configuration of the frame {b} of the mobile base, relative to the frame {s} on the floor, is described by the 3-vector $$q = (\phi, x, y)$$ or the $$SE(3)$$ matrix:

$$
T_{sb}(q) = 
\begin{bmatrix}
cos \phi & -sin \phi & 0 & x \\
sin \phi & cos \phi & 0 & y \\
0 & 0 & 1 & 0.0963 \\
0 & 0 & 0 & 1

\end{bmatrix}

$$

The wheel numbering and forward driving and "free sliding" direction $$\gamma$$ of each wheel is indicated as:

<br>

<figure align = "center">
    <img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/MobileBase.png?raw=true" width="65%"/>
    <figcaption><em>Fig. 2. Top view of the mobile base. 
    <a href="https://hades.mech.northwestern.edu/index.php/File:Yb-book.png" target="_blank">[Image Source]</a></em></figcaption>
</figure>

<br>



## Dynamics Simulation in CoppeliaSim

The CoppeliaSim scene used for the capstone project is the first scene in which we've used CoppeliaSim's ability to simulate bodies in contact. 
A "physics engine" simulates the motions of bodies when forces are applied to them, and when they make contact or collide with each other. 
Physics engines also handle articulated bodies with joints and other constraints.
Here, ODE has been used as the physics engine.

### Rigid Bodies
In CoppeliaSim, each rigid body is classified as either **respondable** or **non-respondable**:

- **respondable**: The body can make contact and collide with other bodies.
- **non-respondable**: The body can pass through other bodies.
Thus if a respondable body and a non-respondable body overlap, or if two non-respondable bodies overlap, there is no collision. If two respondable bodies make contact, however, forces will try to keep them from penetrating each other.

In this scene, only the floor, the gripper fingers, and the cube are respondable. All other bodies are non-respondable.

In addition, each rigid body can be classified as **dynamic** or **non-dynamic**:

- **dynamic**: The body has mass and inertial properties that are used to compute its motion. For example, as shown in the image to the right, you can click on "Cuboid_initial" to learn about the mass properties of the cube in Scene 6. You can also learn about material properties, like "friction values." (The simulated friction coefficient of two bodies in contact is calculated as the product of the friction values of the two materials. This is a simple, but non-physical, way to come up with a friction coefficient for two bodies in contact without having to specify a friction coefficient for every possible pair of materials in contact.)
- **non-dynamic**: The body's motion is not computed according to dynamics computed by the physics engine.

In this scene, only the gripper fingers and the cube are dynamic. 

### Joints
In this scene, each joint is either in **torque/force mode** or **passive mode**:

- **torque/force mode**: In torque/force mode, the motion of the joint is controlled by a torque or force applied by an actuator, possibly governed by a PID control law, for example.
- **passive mode**: In this mode, the joint is kinematically controlled to go to specified positions, without reference to forces or torques.

In this scene, all joints are in passive mode, except for the two joints controlling the gripper motion.
The PID control dynamics for these passive joints has been simulated directly in the trajectory generator.

<br>

## Trajectory Generation

The code pieces together a reference trajectory using various keyframes as a ***Quintic Via Point Trajectory***. 
Let the trajectory be specified by $$k$$ via points, with the start point occurring at $$T_1 = 0$$ and the final point at $$T_k = T$$.
At each via point $$i ∈ {1, . . . , k}$$, the user specifies the desired
position $$β(T_i) = β_i$$ and velocity $$\dot{\beta}(T_i) = \dot{β}_i$$.
The trajectory has $$k −1$$ segments, and the duration of segment $$j ∈ {1, . . . , k − 1}$$ is $$∆T_j = T_j+1 − T_j$$. The joint
trajectory during segment j is expressed as the $$5^{th}$$-order polynomial:

$$
\beta(T_j + ∆t) = a_{j0} + a_{j1}∆t + a_{j2}∆t^2 + a_{j3}∆t^3
$$.

Here,

$$
\begin{align*}
a_{j0} &= β_j,\\
a_{j1} &= \dot{β}_j, \\
a_{j2} &= \frac{3\beta_{j+1} - 3\beta_{j} - 2\dot{\beta_j}∆T_j - 2\dot{\beta}_{j+1}∆T_j}{∆T_j^2}, \\
a_{j3} &= \frac{2\beta_{j} + (\dot{\beta}_{j} + 2\dot{\beta}_{j+1})∆T_j - 2\beta_{j+1}}{∆T_j^3}.
\end{align*}
$$

The 8 segments of the trajectory comprise:

1. Moving the gripper from its initial configuration to a "standoff" configuration above the block.
2. Moving the gripper down to the grasp position.
3. Closing the gripper.
4. Moving the gripper back up to the "standoff" configuration.
5. Moving the gripper to a "standoff" configuration above the final configuration.
6. Moving the gripper to the final configuration of the object.
7. Opening the gripper.
8. Moving the gripper back to the "standoff" configuration.

<br>

<figure align = "center">
    <img src="https://hades.mech.northwestern.edu/images/f/fc/Capstone-traj1.png" width="42%"/>
    <img src="https://hades.mech.northwestern.edu/images/6/68/Capstone-traj2.png" width="42%"/>
</figure>
<figure align = "center">
    <img src="https://hades.mech.northwestern.edu/images/2/2c/Capstone-traj3.png" width="42%"/>
    <img src="https://hades.mech.northwestern.edu/images/f/fe/Capstone-traj4.png" width="42%"/>
</figure>
<figure align = "center">
    <img src="https://hades.mech.northwestern.edu/images/f/f5/Capstone-traj5.png" width="42%"/>
    <img src="https://hades.mech.northwestern.edu/images/a/a2/Capstone-traj6.png" width="42%"/>
</figure>
<figure align = "center">
    <img src="https://hades.mech.northwestern.edu/images/4/4c/Capstone-traj7.png" width="42%"/>
    <img src="https://hades.mech.northwestern.edu/images/1/18/Capstone-traj8.png" width="42%"/>
    <figcaption><em>Fig. 3. The 8 segments of the KUKA youBot's trajectory. This picks the cube up and then places it at another location.
    <a href="https://hades.mech.northwestern.edu/index.php/Mobile_Manipulation_Capstone_2023" target="_blank">[Image Source]</a></em></figcaption>
</figure>

<br>

## Feedback Control

The feedback control law for the system:

$$
\mathcal{V}(t) = [Ad_{X^{-1}X_{d}}]\mathcal{V}_{d}(t) + K_{p}X_{err}(t) + K_i\int_{0}^{t}X_{err}(t)dt 
$$

takes in:

- $$X$$: the *actual* end effector configuration.
- $$X_d$$: the *desired* end effector configuration. And hence, $$X_{err} = log(X^{-1} X_d) $$
- $$X_{d,next}$$: the *desired* configuration at the next time step. And hence, $$[V_d] = (1/∆t)~log(X^{-1}_d X_{d,next})$$ 

to give us the commanded end-effector twist $$\mathcal{V}$$ at every time step.

<br>

<figure align = "center">
    <img src="https://hades.mech.northwestern.edu/images/1/13/Yb-fbtest-new.png" width="65%"/>
    <figcaption><em>Fig. 4. This figure illustrates a test for your FeedbackControl and Jacobian pseudoinverse functions. The current robot configuration is shown, as well as the current end-effector configuration, the current reference end-effector configuration, and the reference end-effector configuration at the next time-step.
    <a href="https://hades.mech.northwestern.edu/index.php/File:Yb-fbtest-new.png" target="_blank">[Image Source]</a></em></figcaption>
</figure>

<br>

To turn this into commanded wheel and arm joint speeds $$(u, \dot{\theta})$$, we use the pseudoinverse of the mobile manipulator Jacobian $$J_e(\theta)$$:

$$
\begin{bmatrix}
u \\
\dot{\theta}
\end{bmatrix}
=
J^{\dagger}_e(\theta) \dot{\theta}
$$

<br>

## Reference

1. [Mobile Manipulation Capstone 2023 - Northwestern Mechatronics Wiki](https://hades.mech.northwestern.edu/index.php/Mobile_Manipulation_Capstone_2023){:target="_blank" rel="noopener"}

<br>



---
layout: post
title:  "Optimized Torques for Load Carrying"
categories: [Research, Python, Legged Locomotion, Model-Predictive Control, Nonlinear Optimization, Controls, Dynamic Systems, Simulation, PyBullet, HEBI Daisy Hexapod, Data Structures
]
image: assets/images/Torque-ControlWalking.gif
featured: true
hidden: true
---

Research, Python, Legged Locomotion, Model-Predictive Control, Nonlinear Optimization, Dynamic Systems, HEBI Daisy Hexapod

<br>

<div align="center">
<video width="75%" controls loop autoplay muted>
    <source src="https://github.com/GogiPuttar/adityanairswebsite.github.io/assets/59332714/3b7aff70-48e1-4a76-ab71-8d593b03e74a" type="video/mp4">
</video>
</div>

<br>

## Abstract

The great terrain adaptability of legged robots,
their capacity to distribute forces among their legs and potential
to benefit from manipulating objects like carrying sensing
payloads, makes them particularly suited for load carrying applications. In this paper, we address the problem of optimizing
the joint-torques of articulated legged robots while performing
such physically strenuous tasks. We propose an inexpensive
optimization-based strategy designed to achieve arbitrary robot
configurations while minimizing the commanded torques, to
keep them within the physical limits of each actuator. Specifically, by first advancing our understanding of the relationship
between the ***ground reaction forces at the robot’s feet*** and
***torques in the robot’s joints***, we are able to pose a simple
***minimax optimization problem*** that can be solved online and
rapidly to yield ***optimized torque commands***. In doing so, our
approach is essentially able to manipulate the ground reaction
forces at the robot’s feet to increase/decrease internal moments
in the robot’s body, such that the final torques that must be
commanded by each actuator remains within its limits. We
experimentally validate our approach in static and walking
tests, where a series-elastic hexapod robot carries payloads of
varying weight, and show that our method results in lower
overall torques and lower average individual actuator effort.
This paper is co-authored by [Swasti Khurana](https://swastikh.github.io/){:target="_blank" rel="noopener"} and [Dr. Guillaume Sartoretti](https://www.sartoretti.science/){:target="_blank" rel="noopener"}.

<br>

## Personal Motivation

This paper is the condensed version of close to one year of continuous work at the National University of Singapore's [MARMot Lab](https://marmotlab.org/){:target="_blank" rel="noopener"}. 
When I began as a researcher at MARMot, [Dr. Guillaume Sartoretti](https://www.sartoretti.science/){:target="_blank" rel="noopener"} tasked me with solving the broad problem of "how to make a legged robot walk *better*". 
The cycle this put me on over the next few months, of understanding, failing, and learning (sometimes succeeding!), has been the most life changing experience of mine as a roboticist. 
This transformed me from a novice with a restlessness to leap into the great beyond of this field, to a seasoned roboticist able to ask the right questions and deliver robust solutions. 
I'm eternally grateful to Guillaume for his unwavering faith in me, and for all the friends I made along the way.

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/marmot.jpg?raw=true" width="70%"/>
<figcaption><b><em>(Some of) the MARMot Family :)</em></b></figcaption>
</figure>

<br>

## I. Introduction

Since legged robots typically approach their torque limits during physically demanding tasks, optimizing these actuator-torques becomes essential for smooth operation.
Such tasks are not only limited to walking on unstructured terrain, but also include manipulating large and/or heavy payloads on structured terrain which are required for applications such as construction, assembly, search and rescue etc.
Formally, the fundamental challenge for joint-torque optimization in legged robots can be stated as: preserving the high-level characteristics of walking, while simultaneously minimizing the load on/power consumed by the robot's actuators.
Since most legged robots are articulated (consist of rotational actuators), minimizing the load on the actuators is synonymous with minimizing the torques about the robot's joints.

Constraining the joint-torques of a legged robot within their limits is crucial for operations, and fully depends on the robot's configuration.
This is because the inability of even a single actuator to deliver its assigned torque can cause the robot's body pose to deviate from the desired one, potentially further rearranging the load and putting the remaining actuators at risk. 
This also indicates that a comprehensive understanding of the relations governing the robot's configuration, external wrenches (i.e. ground reaction forces and weight) and joint-torques, is first necessary, and can then be leveraged to optimize joint-torques.

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/YunaCarry.jpg?raw=true"   width="60%"/>
<figcaption><b><em>Fig. 1. 18-DoF series-elastic hexapod robot carrying a payload of 2 kg
(1kg yellow water bottle, plus 1kg metal weight) on top of its body.</em></b></figcaption>
</figure>

<br>

Given that the majority of research on legged walking is either concerned with feedforward or feedback motion tracking, or pure force control of the legs, optimal control of the ground reaction forces has generally been overlooked and have mainly been considered when preventing slippage.
In particular, the multi-dimensional ground reaction force space, together with the robot's constraints, presents an open avenue for optimal control via the supplementary degrees-of-freedom of the overactuated legged robot system.
Relevant to our work, and discovered that cockroaches employ a very relevant strategy while running, in that the front legs brake while the back legs accelerate.
This results in controlled frictional forces at the feet that effectively counteracts the torques in the insect's knees, reducing the effort required to run.
Building upon this intuition, in this work we develop a joint-torque optimization method for legged robots based on controlling the ground reaction forces at the feet, specifically for the task of carrying heavy loads.
While the robot is walking, at each time step, our optimization method takes the desired robot's configuration as input and optimizes joint-torques for every actuator, resulting in the most optimal ground reaction forces i.e., requiring minimal joint-torques to sustain that robot configuration.

Our method is designed to be applicable for any legged robot either statically standing, or walking in a statically stable gait.
Among these, given the numerous advantages that tripod gaits offer in terms of stability and strength, we test our strategy on an 18-DoF HEBI Daisy Hexapod robot walking in a tripod gait.
We achieve this gait using an open-loop Central Pattern Generator (CPG) motion controller, which is based on rhythmic motion controllers in biological systems, given its ease of parameterization and iterative nature.
However, our torque optimization method could be built on top of any arbitrary locomotion controller.

<br>

## II. Background

### A. Kinematics of a Legged Robot

In this work, we consider a bilaterally symmetrical hexapod robot, comprising a central body link and six articulated legs.
Each of the legs, indexed $$i$$ as in Fig. 2., consists of links connected by three actuated joints, namely the base, shoulder, and elbow. 
This grants each end-effector i.e., the foot, three degrees of freedom relative to the body.
For leg $$i$$, we represent the Jacobian matrix in the body frame as $$J_i(\theta_i) \in \mathbb{R}^{6 \times 3}$$ such that:

$$
\begin{equation}
    \mathcal{V}_i = J_i(\theta_i) \theta_i,
\end{equation}
$$

where $$\mathcal{V}_i \in \mathbb{R}^{6}$$ is the twist of the foot and $$\theta_i \in \mathbb{R}^{3}$$ are the joint angles. It also follows that:

$$
\begin{equation}
    \tau_{i} = J_i^{T}(\theta_i) \mathcal{F}_i,
\end{equation}
$$

where $$\tau_{i} \in \mathbb{R}^{3}$$ are the joint-torques required to generate a wrench $$\mathcal{F}_i \in \mathbb{R}^{6}$$ at the foot.
All vector quantities and transformations in this work consider the hexapod's body frame as the frame of reference, which is defined at the center-of-mass of the hexapod and oriented like in Fig.2.

Specific torques $$g_i(\theta_i) \in \mathbb{R}^3$$ must also be applied to counteract the significant weight of the various links and actuators of the robot.
That is, the torque commanded to the actuators not only includes the desired torque output $$\tau_{d_i}$$, depending on the application and external wrenches, but also accounts for the leg's weight and therefore is:

$$
\begin{equation}
    \tau_{i} = \tau_{d_i} + g_i(\theta_i).
\end{equation}
$$

In this work, we neglect the inertial and Coriolis contributions to torques as they are relatively much smaller.
However, note that these terms cannot generally be neglected for more agile/dynamic applications.

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/YunaLegs.jpg?raw=true"   width="70%"/>
<figcaption><b><em>Fig. 2. Hexapod’s body reference frame and orientation of indexed legs.</em></b></figcaption>
</figure>

<br>

### B. Hybrid Motion-Force Control

PID-based *motion control* in the joint-space can be used to output the desired torques needed to let the feet of the robot track a specific trajectory:
The desired torques to be tracked read:

$$
\begin{equation}
    \tau_{d_i} = M_i(\theta_i)\left(K_P \theta_{e_i} + K_D \dot{\theta}_{e_i} + \int_{}^{} K_I \theta_{e_i} dt\,\right),
\end{equation}
$$

where $$M_i(\theta_i) \in \mathbb{R}^{3 \times 3}$$ is the symmetric positive-definite mass matrix of the leg, and $$K_P$$, $$K_I$$, and $$K_D$$ are the respective PID control gains, which effectively translate joint-space errors into torque commands.
That is because, $$\theta_{e_i} = \theta_{d_i} - \theta_{i}$$ represents the error between the desired angle vector $$\theta_{d_i}$$ (obtained through inverse kinematics of the required leg pose $$X_{d_i} \in \mathbb{R}^6$$) and the current angle vector $$\theta_i$$.

On the other hand, feedforward *force control* offers a natural means to let foot $$i$$ exert a specific wrench $$\mathcal{F}_{d_i}$$ onto the environment:

$$
\begin{equation}
    \tau_{d_i} = J_i^{T}(\theta_i) \mathcal{F}_{d_i}.
\end{equation}
$$

We note that pure motion control generally requires the end-effector to accurately produce arbitrary velocities and is therefore better suited to environments where there is no resistance to the end-effector's motion along any direction. 
On the other hand, pure force control is only possible if the environment provides resistance forces along each direction.
In some environments, resistance is provided only along certain directions which may limit the use of either one of these strategies i.e., motion control would be sacrificed along directions where the end-effector cannot move, or force control would be sacrificed along directions where the end-effector is free to move.
In such cases, we can effectively utilize all available degrees of actuation through *hybrid motion-force control*.
This strategy combines both approaches, by generating controlled motion along free directions and controlled forces along resisted direction, and can be implemented as:

$$
\begin{equation}
\label{Eq6}
\begin{split}
    &\tau_{d_i}=\\
    &J_i^{T}(\theta_i)\biggl(\underbrace{P(\theta_i)\left(\Lambda(\theta_i)\left(K_P \theta_{e_i} + K_D \dot{\theta}_{e_i} + \int_{}^{} K_I \theta_{e_i} dt\right)\right)}_{\text{motion control}}\\
    & + \underbrace{(I - P(\theta_i))\left(\mathcal{F}_{d_i}\right)}_{\text{force control}}\biggr),
\end{split}
\end{equation}
$$

where $$\Lambda(\theta_i) = (J_i^{T})^{+}M_i(\theta_i)$$, in which $$(J_i^{T})^{+} \in \mathbb{R}^{6 \times 3}$$ is the Moore-Penrose pseudoinverse of $$J_i^{T}$$, and $$P(\theta_i) \in \mathbb{R}^{6 \times 6}$$ represents the natural constraints imposed on the end-effector by the environment.
For example, when the end-effector is completely free, $$P(\theta_i) = I$$ has rank 6, whereas when it cannot move, $$P(\theta_i) = O$$ has rank 0.
When the end-effector has restricted movement along some $$k$$ directions, $$P(\theta_i)$$'s diagonal is populated with zeroes for these $$k$$ directions and ones for the remaining $$6 - k$$ directions. 
Therefore, $$P(\theta_i)$$ is rank $$6 - k$$, and $$I - P(\theta_i)$$ is rank $$k$$. 
Thus, $$P(\theta_i)$$ partitions the $$6$$-dimensional force space into wrenches that address the motion control task and wrenches that address the force control task.

<br>

## III. Problem Analysis

In this section, we derive important relationships among the hexapod's configuration, its ground reaction forces, and joint-torques in order to define our optimization method.
We start by explaining the relevance of hybrid motion-force control to walking robots, considering the foot-ground interface.
We then advance our understanding of the foot-ground interface by conducting a static analysis of the entire hexapod (including any payload), and estimating the resulting torque load on each joint.
Finally, we combine these tools and analyses to formulate and solve an optimization problem, and explain how this translates to optimal locomotion for load carrying operations. 

### A. Hybrid Motion-Force Control in Hexapedal Locomotion

In particular, for our case of a hexapod walking in a statically stable tripod gait, we observe two scenarios where hybrid motion-force control can be utilized.
While walking on flat ground, at any given moment, there are ideally three legs in the air (swing stage) and three legs on the ground (stance stage) given our alternate-tripod gait.
The legs in swing stage face no restriction from the environment and thus have their natural constraints matrix $$P(\theta_i) = I$$. 
The legs in stance stage, however, are restricted due to their contact with the ground.
By modelling the foot-ground interface as a point contact with friction $$\{\textbf{S}(C)\}$$, we obtain: 

$$
\begin{equation}
    P(\theta_i) = 
    \begin{bmatrix}
    0 & 0 & 0 & 0 & 0 & 0 \\
    0 & 0 & 0 & 0 & 0 & 0 \\
    0 & 0 & 0 & 0 & 0 & 0 \\
    0 & 0 & 0 & 1 & 0 & 0 \\
    0 & 0 & 0 & 0 & 1 & 0 \\
    0 & 0 & 0 & 0 & 0 & 1 \\
    \end{bmatrix}.
\end{equation}
$$

This is a suitable approximation, not only for our hexapod whose feet have a rounded mushroom-like shape, but also for arbitrary multi-legged robots whose foot ground interfaces are generally much smaller than their overall size.
As illustrated in Fig. 3., this implies that the foot has three rotational degrees of freedom i.e., it cannot slide on the ground or penetrate it, however it can wobble and spin about the point of contact.

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/YunaFoot.png?raw=true"   width="50%"/>
<figcaption><b><em>Fig. 3. Foot-ground interface during the stance phase of a leg.</em></b></figcaption>
</figure>

<br>

### B. Static Analysis and Ground Reaction Forces

In Fig. 4., we see that when the hexapod is standing stationary on three legs (indexed $$i = \{1, 2, 3\}$$), each foot at position $$(x_i, y_i, z_i)$$ experiences a normal reaction $$N_i$$ along the $$+z$$ axis, and friction $$f_{x_i}$$ and $$f_{y_i}$$ along the $$+x$$ and $$+y$$ axes respectively.
Since the robot's body link is at equilibrium with respect to the ground for translation along the $$z$$ axis, and rotation about the $$x$$ and $$y$$ axes (roll-pitch), by balancing external forces and torques we obtain:

$$
\begin{equation}
    \begin{bmatrix}
        1   & 1   & 1\\
        x_1 & x_2 & x_3\\
        y_1 & y_2 & y_3
    \end{bmatrix}
    \begin{bmatrix}
        N_1\\
        N_2\\
        N_3
    \end{bmatrix}
    =
    \begin{bmatrix}
        W_{robot} + W_{load}\\
        0\\
        0
    \end{bmatrix},
\end{equation}
$$

where $$W_{robot}$$ is the combined weight of the hexapod's body and legs, and $$W_{load}$$ is the weight of the robot's payload (assumed to be placed at the robot's center-of-mass) which simply behaves like extra body weight.

Through the robot's model and position feedback, we know the position and mass of every link of the hexapod and can simply obtain the normal reaction forces as:

$$
\begin{equation}
    \begin{bmatrix}
        N_1\\
        N_2\\
        N_3
    \end{bmatrix}
    =
    \begin{bmatrix}
        1   & 1   & 1\\
        x_1 & x_2 & x_3\\
        y_1 & y_2 & y_3
    \end{bmatrix}^{-1}
    \begin{bmatrix}
        W_{robot} + W_{load}\\
        0\\
        0
    \end{bmatrix}.
\end{equation}
$$

Note that this inverse exists when the support polygon defined by the three feet has some non-zero area (which holds true for statically-stable gaits).

Similarly, as the body link is at equilibrium for translation along the $$x$$ and $$y$$ axes, and for rotation about the $$z$$ axis (yaw), we obtain three constraints on friction:

$$
\begin{equation}
    \begin{bmatrix}
        1 & 1 & 1 & 0 & 0 & 0\\
        0 & 0 & 0 & 1 & 1 & 1\\
        y_1 & y_2 & y_3 & -x_1 & -x_2 & -x_3
    \end{bmatrix}
    \begin{bmatrix}
        f_{x_1}\\
        f_{x_2}\\
        f_{x_3}\\
        f_{y_1}\\
        f_{y_2}\\
        f_{y_3}
    \end{bmatrix}
    =
    \begin{bmatrix}
        0\\
        0\\
        0
    \end{bmatrix}.
\end{equation}
$$

Through our analysis we can infer that, for any given configuration of the hexapod, the normal reaction forces are determinate, while the friction forces lie on a 3-dimensional surface within the 6-dimensional frictional force space.
It is these supernumery degrees-of-freedom of the hexapod, which we exploit to optimize joint torques. 
Therefore, we manipulate the ground reaction forces at the robot's feet to increase/decrease internal moments in the robot's body, such that the final torques that must be commanded by each actuator remains within its limits.

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/YunaFull.png?raw=true"   width="70%"/>
<figcaption><b><em>Fig. 4. External forces acting on the robot in the static case.</em></b></figcaption>
</figure>

<br>

### C. Joint-Torque Estimation

We now determine the torques generated at each joint given a desired configuration of the hexapod and a set of ground reaction forces (in practice, measured from sensors on the robot).
As shown in Fig. 5., for any stance leg, the torque $$\tau_{a_i}$$ due to ground reaction forces, about any unit vector $$(u_i, v_i, w_i)$$ situated at $$(x_{a_i}, y_{a_i}, z_{a_i})$$ is:

$$
\begin{equation}
    \tau_{a_i} = 
    \begin{pmatrix}
        x_i - x_{a_i}\\
        y_i - y_{a_i}\\
        z_i - z_{a_i}
    \end{pmatrix}
    \times
    \begin{pmatrix}
        f_{x_i}\\
        f_{y_i}\\
        N_i
    \end{pmatrix}
    \cdot
    \begin{pmatrix}
        u_i\\
        v_i\\
        w_i
    \end{pmatrix}.
\end{equation}
$$

As we substitute $$(x_{a_i}, y_{a_i}, z_{a_i})$$ with the location of each actuator, and $(u_i, v_i, w_i)$ with the positive joint axis of that actuator, we are able to deduce the base $$\tau_{b_i}$$, shoulder $$\tau_{s_i}$$, and elbow $$\tau_{e_i}$$ joint-torques as:

$$
\begin{align}
    \tau_{b_i} &=
    \begin{pmatrix}
        x_i - x_{b_i}\\
        y_i - y_{b_i}\\
        z_i - z_{b_i}
    \end{pmatrix}
    \times
    \begin{pmatrix}
        f_{x_i}\\
        f_{y_i}\\
        N_i
    \end{pmatrix}
    \cdot
    \begin{pmatrix}
        0\\
        0\\
        1
    \end{pmatrix}\\
    \tau_{s_i} &=
    \begin{pmatrix}
        x_i - x_{s_i}\\
        y_i - y_{s_i}\\
        z_i - z_{s_i}
    \end{pmatrix}
    \times
    \begin{pmatrix}
        f_{x_i}\\
        f_{y_i}\\
        N_i
    \end{pmatrix}
    \cdot
    \begin{pmatrix}
        sin(\theta_{b_i})\\
        -cos(\theta_{b_i})\\
        0
    \end{pmatrix}\\
    \tau_{e_i} &= 
    \begin{pmatrix}
        x_i - x_{e_i}\\
        y_i - y_{e_i}\\
        z_i - z_{e_i}
    \end{pmatrix}
    \times
    \begin{pmatrix}
        f_{x_i}\\
        f_{y_i}\\
        N_i
    \end{pmatrix}
    \cdot
    \begin{pmatrix}
        sin(\theta_{b_i})\\
        -cos(\theta_{b_i})\\
        0
    \end{pmatrix},
\end{align}
$$

where $$(x_{b_i}, y_{b_i}, z_{b_i})$$, $$(x_{s_i}, y_{s_i}, z_{s_i})$$, $$(x_{e_i}, y_{e_i}, z_{e_i})$$ are the respective positions of the base, shoulder, and elbow actuators, while $$\theta_{b_i}$$ is the base joint angle.
Note that these equations for joint-torques are linear in terms of $$(f_{x_i}$$, $$f_{y_i}$$, and $$N_{i})$$, whose coefficients are functions of quantities given by the hexapod's mass distribution.

<figure align = "center"><img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/YunaGRF.jpg?raw=true"   width="70%"/>
<figcaption><b><em>Fig. 5. Ground reaction forces and torque generated about an example
actuator (here, the front left elbow actuator).</em></b></figcaption>
</figure>

<br>

### D. Optimal Control

We first define a dimensionless term known as the normalized joint-torque $$\tilde{\tau}_{a_i}$$, i.e., the absolute value of the joint-torque $$\tau_{a_i}$$ divided by the maximum torque the actuator can generate $$\tau_{a, max_i}$$:

$$
\begin{equation}
    \tilde{\tau}_{a_i} = \left|\frac {\tau_{a_i}}{\tau_{a, max_i}}\right|.
\end{equation}
$$

In order for the hexapod to be functioning within its torque limits, the normalized joint-torque of all its actuators must lie between 0 and 1, at every time step. 
We further note that, the lower the normalized joint-torque of the most strained actuator (maximum normalized torque), the farther all of the hexapod's actuators are from exceeding their limits.
Thus, we can define our optimization problem as a typical Minimax problem:

$$
\begin{align}
   &\min_{f_{x_1},f_{x_2},f_{x_3},f_{y_1},f_{y_1},f_{y_3},}
   \begin{aligned}[t]
      &Z \\
   \end{aligned} \notag \\
   \\
   \text{subject to:} \notag \\
   & \begin{bmatrix}
        1 & 1 & 1 & 0 & 0 & 0\\
        0 & 0 & 0 & 1 & 1 & 1\\
        y_1 & y_2 & y_3 & -x_1 & -x_2 & -x_3
    \end{bmatrix}
    \begin{bmatrix}
        f_{x_1}\\
        f_{x_2}\\
        f_{x_3}\\
        f_{y_1}\\
        f_{y_2}\\
        f_{y_3}
    \end{bmatrix}
    =
    \begin{bmatrix}
        0\\
        0\\
        0
    \end{bmatrix},
\end{align}
$$

where the cost function reads:

$$
\begin{equation}
    Z = \text{max}(\tilde{\tau}_{b_1}, \tilde{\tau}_{s_1}, \tilde{\tau}_{e_1},\\
    \tilde{\tau}_{b_2}, \tilde{\tau}_{s_2}, \tilde{\tau}_{e_2},\\
    \tilde{\tau}_{b_3}, \tilde{\tau}_{s_3}, \tilde{\tau}_{e_3}),
\end{equation}
$$

since we already established that given any configuration of the hexapod, the joint-torques (and subsequently $$Z$$) are a function of the frictional forces.

In practice, we solve this constrained optimization problem over the 3-dimensional surface within the 6-dimensional frictional force space, by relying on the *trust region method*.
This iterative optimization method essentially works by approximating a portion of the cost function, within a given neighborhood (trust region) of the current point, using a quadratic modelling function.
The optimal value is searched for within this trust region, subject to the constraints.
If the model accurately represents the cost function, the trust region is expanded; conversely, an inaccurate model results in a contraction of the trust region.
This iterative process is terminated when the size of the trust region falls below an appropriate tolerance, indicating convergence to a (local) minimum. 

Finally, the desired output wrench of the stance feet $$\mathcal{F}_{d_i}$$ is constructed using the determined normal reactions $$N_i$$, and predicted optimal friction values $$f_{x_i}$$ and $$f_{y_i}$$:

$$
\begin{equation}
    \mathcal{F}_{d_i} = 
    \begin{bmatrix}
        -f_{x_i}\\
        -f_{y_i}\\
        -N_i\\
        0\\
        0\\
        0
    \end{bmatrix},
\end{equation}
$$

which when plugged into Eq. 6, with the appropriate natural constraints matrix $$P(\theta_i)$$, yield the optimal desired joint-torques for every actuator of the robot. 

<br>

## IV. Hardware Experiments

### A. Robot Description and CPG Walking
We experimentally validate our optimization method on a HEBI Daisy Hexapod Robot with 18 series-elastic actuators.
Within each actuator, the series-elastic element allow us to accurately sense the external torque applied by the environment at every time step.
Since, for an actuator at equilibrium, this external/environmental torque is equal and opposite to the commanded joint-torque, this quantifies the effort required in motion.
For our hexapod, the base, shoulder, and elbow actuators have maximum continuous torques as 8 Nm, 16 Nm, and 8 Nm respectively.
Therefore, the normalized torques read:

$$
\begin{align}
    \tilde{\tau}_{b_i} = \frac {\left|\tau_{b_i}\right|}{8} \quad \quad
    \tilde{\tau}_{s_i} = \frac {\left|\tau_{s_i}\right|}{16} \quad \quad
    \tilde{\tau}_{e_i} = \frac {\left|\tau_{e_i}\right|}{8},
\end{align}
$$

where all torque measurements are in Nm.
The weight of the robot $$W_{robot} \approx 210~ \textnormal{N}$$: 75 N for the body, and 135 N for all legs.
Tripod gait walking is implemented using the low-level Central Pattern Generator (CPG) task-space motion controller, parameterized with: the center of each foot's limit cycle i.e. grasp map, the step size, forcing to limit cycle, coupling strength, and coupling matrix for tripod gait.

### B. Iterative Trust Region Optimization
To solve the optimal control problem, we use the `minimize(method='trust-constr')` function of the `scipy.optimize` library in python3.
Using this, a typical one-shot optimization takes approximately 200 milliseconds to converge.
However, this computing times prevents us from running this optimization at every time step of the hexapod's sensing-actuation cycles, as they are programmed to operate at 50 Hz (20 ms per time step).
To improve this, we first capitalize on the fact that the cost function surface, is polygonal in nature, consisting of piecewise linear surfaces.
Therefore, any sufficiently local approximation of this surface is linear, implying that we can initialize the Hessian matrix as a zero matrix for all trust region searches, thereby reducing the typical convergence time to approximately 45 ms.
Second, by assuming that the robot's motion does not considerably change the cost function within the convergence window, we can decompose the optimization iterations over the sensing-actuation cycles of the hexapod such that only one iteration of the trust region method takes place per time step. 
This makes the approach more robust, as it not only enables us to parallelly update the optimization problem and compute optimal values at each time step, but also reduces the computation time per iteration to just 2 ms.
The iterative nature of our approach also makes it suitable for implementation with our underlying CPG controller, since joint-torque optimization can simply be incorporated as another set of state-space variables within the first-order dynamical model of the CPG.

### C. Experimental Procedure
We tested our hexapod's motion in three scenarios, namely: walking with no load, walking with a 2 kg load, and standing with a 5 kg load.
All loads are placed on top of the body link of the hexapod, approximately at the hexapod's centre-of-mass.
The 2 kg and 5 kg loads increase the body's weight to 120% and 160% respectively.
For each scenario, we record the external torques in the actuators with and without our optimization strategy.
For the walking cases, the torques are recorded over the duration of precisely one step.

<br>

## V. Conclusion

In this paper, we addressed the problem of joint-torque optimization during strenuous tasks in legged robots by proposing an optimization method that manipulates the ground reaction forces to create internal moments with the robot system to minimize joint-torques. 
To this end, we developed a detailed understanding of the relationship between the joint-torques, ground reaction forces, and robot configuration.
Further, we developed and validated a framework for adapting the trust region search method to the iterative first-order CPG dynamical system, by decomposing its iterations over the CPG time steps. 
We then experimentally validated our optimization strategy on a series-elastic hexapod robot, walking without a load, walking with a 2 kg load, and standing with a 5 kg load. Through our results, we inferred that our optimization method is prone to joint-torques overshooting their optimal values. 
We found that our approach results in overall reduced torques for the robot in all cases, and significantly reduced normalized torques while walking with a 2 kg load.
The "Results and Discussion" section has currently been omitted from this webpage.

<br>


---
layout: post
title:  "Navigation and Kalman Filter Localization of Self Driving Car"
categories: [Python, Motion Planning, Controls, Simulation, Signals, Wheeled Locomotion, Dynamic Systems, Webots, Data Structures

]
image: assets/images/KF.gif
featured: true
hidden: true
---

Python, Motion Planning, Controls, Signals, Wheeled Locomotion, Webots

## Introduction

<div align="center"><h2> <a href="https://github.com/GogiPuttar/Four-wheeled-robot-localization-with-Kalman-Filter">View it on Github ⇗</a></h2></div>

## Personal Motivation 

## Design 

### Sensors

## Control

### Steering

### Waypoint Navigation

## Localization with Extended Kalman Filter

A Kalman Filter is a Bayes’ Filter for Gaussian Linear Case that relies on recursive state
estimation to make it a popular estimation algorithm used for a variety of purposes ranging
from temperature control to sensor fusion. The algorithm is relatively simple to implement and
requires minimal computational power making it suitable for this project. However a fundamental
understanding of the basic concepts is very necessary for its implementation. For this project we
have used an Extended Kalman Filter to incorporate multiple degrees of freedom of our sensors.
Here's a step-by-step description of our implementation:

1. **Initialization:**
For the first iteration of the Extended Kalman Filter, we start at time $$k$$. 
We initialize the state vector and control vector for the previous time step $$k − 1$$. 
|We have also initialized the control
state variables and assigned them an initial value of $$0$$. 

$$
\begin{equation*}
    \hat{x}_{k-1|k-1} = \begin{bmatrix}
x_{k-1} \\
y_{k-1}\\
\gamma_{k-1} 
\end{bmatrix} = \begin{bmatrix}
 0 \\
 0 \\
 0
\end{bmatrix} 
\end{equation*},
$$

$$
\begin{equation*}
    u_{k-1} = \begin{bmatrix}
v_{k-1} \\
w_{k-1}

\end{bmatrix} = \begin{bmatrix}
 0 \\
 0
\end{bmatrix} 
\end{equation*},
$$

2. **Predicted State Estimation:**
This estimated state prediction for time $$k$$ is currently our best guess of the current state of the bot.
We can also add some static noise values to make the sensor measurements more realistic. 
However, for the purpose of implementing localization with maximum possible precision, we have assumed noise from measurements to be negligible. 

$$
\begin{equation*}
    \begin{bmatrix}
x_{k} \\
y_{k}\\
\gamma_{k} 
\end{bmatrix} = \begin{bmatrix}
1 & 0 & 0\\
0 & 1 & 0\\
0 & 0 & 1 
\end{bmatrix} \begin{bmatrix}
x_{k-1} \\
y_{k-1}\\
\gamma_{k-1} 
\end{bmatrix} + \begin{bmatrix}
 \cos{\gamma_{k-1} }*dk & 0 \\
 \sin{\gamma_{k-1} }*dk & 0 \\
 0 & dk
\end{bmatrix} \begin{bmatrix}
v_{k-1} \\
w_{k-1}

\end{bmatrix} + \begin{bmatrix}
noise_{k-1} \\
noise_{k-1}\\
noise_{k-1} 
\end{bmatrix}
\end{equation*} \\
$$

3. **Predicted Covariance of State Estimate:**
Since our current predicted state estimate for time $$k$$ is not completely accurate, we now determine the state covariance matrix $$P_{k|k-1}$$ for the current time step . 
Since our robot car has three state variables $$[x, y, \gamma]$$, $$P$$ is a $$3 \times 3$$ matrix, whose diagonal terms denote the variance, and off-diagonal terms denote the covariance. 
Further, $$Q_k$$ which is the $$3 \times 3$$ state model noise covariance matrix i.e., the deviation of the actual motion from the space state model:

$$
\begin{equation*}
    Q_k = \begin{bmatrix}
Cov(x, x) & Cov(x, y) & Cov(x, \gamma)\\
Cov(y, x) & Cov(y, y) & Cov(y, \gamma)\\
Cov(\gamma, x) & Cov(\gamma, y) & Cov(\gamma, \gamma) 
\end{bmatrix}
\end{equation*}
$$

4. **Innovation/Measurement Residual:**
The measurement residual $$\tilde{y}_k$$, is simply the difference between the observation vector $$z_k$$, and the observation model $$h(\hat{x}_{k|k-1})$$. 
The observation vector contains the actual readings from our sensors at time $$k$$, and the observation model contains the predicted state mesurements at time $$k$$.

$$
\begin{equation*}
    \tilde{y}_k = z_k- h(\hat{x}_{k|k-1})
\end{equation*}
$$

5. **Innovation (or residual) Covariance:**
Here we have used the predicted covariance of the state estimate $$P_{k|k-1}$$ from Step $$3$$ and the measurement matrix $$H_k$$ and its transpose, and $$R_k$$ (sensor measurement noise covariance matrix)
to calculate $S_k$, which represents the measurement residual covariance (also known as measurement prediction covariance).

$$
\begin{equation*}
    \mathbf{S}_k = \mathbf{H}_k\mathbf{P}_{k | k-1}{\mathbf{H}_k}^T -  h(\hat{x}_{k|k-1}) + \mathbf{R}_k
\end{equation*}
$$

6. **Near-Optimal Kalman Gain:**
his is effectively our parameter to compute the weighted average between our actual readings and the predicted values.
When the sensor noise is large, it approaches $$0$$ and when the control state noise is large it approaches $$1$$.
Thus, it allows us to estimate the most accurate readings and uses those values to update itself in the next iteration.

$$
\begin{equation*}
 \mathbf{K}_k = \mathbf{P}_{k | k-1}{\mathbf{H}_k}^T{\mathbf{S}_k}^{-1}
\end{equation*}
$$

7. **Updated State Estimation:**
Here we finally compute the weighted average mentioned in the previous step.
We use the predicted state values, the Kalman gain and the measurement residual to do so.

$$
\begin{equation*}
\mathbf{\hat{x}_{k|k}} = \mathbf{\hat{x}_{k|k-1}} + \mathbf{K}_k \mathbf{\tilde{y}_k}
\end{equation*}
$$

8. **Updated Covariance of the State Estimate:**
Finally, we correct the estimated covariance for the timestep k by updating it.
We use the near-optimal Kalman gain, the measurement matrix and the initially assumed values of the covariance for this timestep.

$$
\begin{equation*}
    \mathbf{P_{k|k} = (I-K_{k}H_{k})~P_{k|k-1}}
\end{equation*}
$$

## Path Planning ($$A*$$ Algorithm)

Path-planning is an important primitive for autonomous mobile robots that lets robots and the shortest (or otherwise optimal) path between two points. Optimal paths could be paths that minimize the amount of turning, the amount of braking or some other metric a specific
application requires. In this project, the path is optimized in terms of the distance traveled by the path.

For our path planning algorithm and navigation, we have put together some code from some open source libraries to implement the $$A*$$ algorithm.

Here, $$n$$ is the current node, and $$n_0$$ is the adjacent node. The accumulated cost from the start node to any given node $$n$$ with $$g(n)$$. The cost from a node $n$ to an adjacent node $$n_0$$ becomes $$c(n; n_0 )$$, and the expected cost (heuristic cost) from a node $$n$$ to the goal node is described with $h(n)$. The total expected cost from start to goal via state n can then be written as:

$$
f(n) = g(n) + h(n),
$$

where $$h(n)$$ is a parameter that denotes an algorithm-dependent heuristic. For the $$A*$$ algorithm, this value is $$0$$.

<figure align = "center">
<img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/SelfDrivingCarAStar.png?raw=true"  width="65%"/>
<figcaption><em>Fig. 3: MATLAB plot of our path planner in a sample environment.</em></figcaption>
</figure>

$$A*$$ works by making a lowest-cost path tree from the start node to the target node. 
$$A*$$ algorithm begins at the start (yellow star), and considers all adjacent cells. 
Once the list of adjacent cells has been populated, it filters out those which are inaccessible (walls, obstacles, out of bounds). 
It then picks the cell with the lowest cost, which is the estimated $$f (n)$$. 
This process is recursively repeated until the shortest path has been found to the target (red star). 
The computation of $$f(n)$$ is done via a heuristic that usually gives good results. 
$$A*$$ differs from an algorithm where a heuristic is more of an estimate and is not necessarily correct.
Famous examples of heuristics are the ”Euclidean” and the ”Manhattan” distance. 
The heuristic function must be admissible, which means it can never overestimate the cost to reach the goal.
Also, the value of $$h(n)$$ would ideally equal the exact cost of reaching the destination. 
This is, however, not possible because the best path is not known. 
Here, we have chosen the Manhattan distance given its advantages in grid based map representations.
This in turn, generates a sequence of waypoints which the controller can follow.


## Conclusion and Future Work

We have modelled a robust 4-wheeled robot capable of traveling on uneven rigid surfaces with reasonable accuracy. 
Our controller can make the 4-wheeled, non-holonomic robot travel on any path by just giving it a list of waypoints.
The controller can also incorporate the A* Algorithm for avoiding obstacles. 
This in-turn makes the robot capable of autonomously navigating through any course with mapped obstacles. 
We have developed an Extended Kalman Filter suitable for accurate localization and pose estimation of our 4-wheeled robot, enabling it to overcome the physical limitations of its sensors, so that it can travel in statically uneven and non-ideal environments.

Future improvements and augmentations in our project may include: Inclusion of Trajectory error and Pose error matrices to quantify the performance of Localization through the Kalman-filtering algorithm.
Capability of detecting and avoiding transient obstacles through techniques such as SLAM. 
This can be done through the incorporation of a LIDAR sensor or a camera and would be helpful for extending the capabilities of the robot to transient and deformable environments.
Incorporation a PID control setup for smoother steering of the robot. 
Currently, the robot has to stop every time it changes the steering angle of its wheels. 
The implementation of a PID controller in our algorithm will allow the robot to change direction without stopping. This will also prevent sudden jerks and vibrations that might be noisy for the accelerometer. 
There are various assumptions made by the software during a simulation. Real world application will involve many other considerations. 
Fabrication of a real-life model based on our current one will help us tackle a lot of these first hand and should be the next step in further development of our project.


### This Post is under construction 🛠️
Over the winter break, I will be adding more information about my personal and professional projects. 
If you are interested, please check back on Janurary 14th, 2024.

In the meanwhile, the post on [**Dexterous Manipulation through Virtual Reality**](https://adityanairs.website/DexterousManipulationThroughVR/) is complete and ready for viewing.




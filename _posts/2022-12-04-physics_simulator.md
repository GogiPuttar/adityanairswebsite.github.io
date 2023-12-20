---
layout: post
title:  "Physics Simulation"
categories: [ Python, Dynamic Systems, Simulation, Jupyter Notebook ]
image: assets/images/cropped_jack.gif
featured: false
hidden: false
---
Python, Dynamic Systems, Simulation, Jupyter Notebook


<div align="center"><img src="https://raw.githubusercontent.com/oubrejames/oubrejames.github.io/4b9a9bb1b282e894d9406c93f0adbf26e34a60b5/assets/images/cropped_jack.gif" alt="jack_box_gif" width="600"/></div>

Using Lagrangian dynamics, I was able to simulate a box bouncing around within a larger box. First,
I calculated the potential and kinetic energies of the system to obtain its Lagrangian and later on,
the Euler-Lagrange equations. These equations describe the dynamics of the system while not
experiencing impact. To detect impact the following coordinate frames were used.


<div align="center"><img src="https://user-images.githubusercontent.com/46512429/208279053-1a1dd404-148d-4dbd-aadc-0b7c9d14c366.png" alt="jack_box_gif" width="500"/></div>

Using the transformation matrices between these frames, I am able to detect impact if any of the 
smaller box's vertices intersect the larger box's walls. Each vertex is checked against each wall 
for every time step of the simulation. If an impact is detected, the dynamic equations are updated and
the simulation continues.

<div align="center"><h2> <a href="https://github.com/oubrejames/physics-simulator">View it on Github</a></h2></div>

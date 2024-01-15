---
layout: post
title:  "Maze Solver"
categories: [Python, Motion Planning
]
image: assets/images/MazeSolverFinal.gif
featured: false
hidden: false
---

Python, Motion Planning

<!-- Note to self: Added multiple starting points in the video! -->

<div align="center">
<video width="65%" controls loop autoplay muted>
    <source src="https://github.com/GogiPuttar/adityanairswebsite.github.io/assets/59332714/4f124db2-c6fe-415d-9a1a-28966b6713ab" type="video/mp4">
</video>
</div>

<br>

## Overview

This is my implementation of the [Wavefront Planner](https://nu-msr.github.io/hackathon/wavefront.html) maze solving algorithm. 
Here, an Agent is manually moved around an M x N grid maze, and at any point can summon the Oracle to show them the path to the goal.
Further, the entire interface is in a terminal and the walls of the maze are rendered off a bitmap, with unicode.

<br>

## Personal Motivation

I completed this project during Northwestern's MS in Robotics Hackathon. 
In a span of two weeks, we undertook various projects involving Randomly-exploring Random Trees (RRTs), computer vision, manipulation, path planning, and shell scripting in an extremely fun, gamified environment. 

## Wavefront Planner

A wavefront planner is a breadth-first technique for finding paths in a maze.
It provides the shortest path from any starting location to a given goal.
It also requires full knowledge of the maze.



<br>

## Conclusion

This project fulfills its intended goal in a very pretty manner.
The reason I have a separate post for this relatively short project is largely because it looks asthetically pleasing.





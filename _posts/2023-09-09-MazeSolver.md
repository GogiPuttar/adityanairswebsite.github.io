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

<br>

<div align="center">
<video width="65%" controls loop autoplay muted>
    <source src="https://github.com/GogiPuttar/adityanairswebsite.github.io/assets/59332714/4f124db2-c6fe-415d-9a1a-28966b6713ab" type="video/mp4">
</video>
</div>

<br>

## Overview

This is my implementation of the [Wavefront Planner](https://nu-msr.github.io/hackathon/wavefront.html){:target="_blank" rel="noopener"} maze solving algorithm. 
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

1. **SETUP**

    <figure align = "center">
    <img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/MazeSetup.png?raw=true" width="65%"/>
    <figcaption><em>Fig. 1. The Goal is the red square. The robot starts at the green square and knows the full layout of the maze.</em></figcaption>
    </figure>

    <br>

2. **FIRST STEP**

    <figure align = "center">
    <img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/MazeFirstStep.png?raw=true" width="65%"/>
    <figcaption><em>Fig. 2. Start at the goal. It is 0 squares from the goal so label it 0.</em></figcaption>
    </figure>

    <br>

3. **FIRST EXPANSION**

    <figure align = "center">
    <img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/MazeFirstExpansion.png?raw=true" width="65%"/>
    <figcaption><em>Fig. 3. All the neighbors of the goal are a distance of 1 from the goal. (In this example the robot cannot move diagonally).</em></figcaption>
    </figure>

    <br>

4. **NEXT EXPANSION**

    <figure align = "center">
    <img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/MazeNextExpansion.png?raw=true" width="65%"/>
    <figcaption><em>Fig. 4. All the neighbors of the 1-distance squares are a distance of 2 from the goal.</em></figcaption>
    </figure>

    <br>

5. **SUBSEQUENT EXPANSIONS**

    <figure align = "center">
    <img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/MazeSubsequentExpansions.png?raw=true" width="65%"/>
    <figcaption><em>Fig. 5. Keep expanding until every square is labeled with its distance from the goal.</em></figcaption>
    </figure>

    <br>

6. **USING THE PLAN**

    <figure align = "center">
    <img src="https://github.com/GogiPuttar/adityanairswebsite.github.io/blob/main/assets/images/MazeUsingThePlan.png?raw=true" width="65%"/>
    <figcaption><em>Fig. 6. Starting at any square (say the green one) the robot moves to the neighbor that is closest to the goal.</em></figcaption>
    </figure>


<br>

## Conclusion

This project fulfills its intended goal in a very pretty manner.
The reason I have a separate post for this relatively short project is largely because it looks asthetically pleasing.

<br>

## References

1. [MSR Hackathon: a-Maze-ing Challenge](https://nu-msr.github.io/hackathon/python_challenge.html){:target="_blank" rel="noopener"}

<br>


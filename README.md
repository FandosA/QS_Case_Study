# Technical Case Study – Robotics & Computer Vision
## Overview

This repository contains solutions and analysis for a technical case study focused on:
1. Multithreading in C++
2. Algorithmic problem solving in Python
3. Robotics perception and control (UAV capture scenario)

## C++ Task – Multithreading Analysis
The program launches two threads executing repetitive tasks until a shared stop condition is met.

```StartThread``` is a wrapper that runs a function (```Process```) in a loop. Execution stops when ```Process()``` returns ```true``` or a timeout is reached. A shared atomic flag (```running```) controls termination of both threads.

### Behaviour
```Thread 1``` executes every 2 seconds and increments a counter. ```Thread 2``` executes every 1 second and stops after 5 iterations. Since both threads share the running flag, when one stops, the other stops as well. So, ```Thread 2``` will finish after ~5 seconds (5 iterations) and then ```Thread 1``` will stop too, having performed ~2–3 iterations in that time.

### Issues Identified
1. Dangling Reference (Critical Bug): the Process function was passed by reference while being a temporary object, leading to undefined behavior. To fix it, the ```Process```function must be passed by value instead of by reference.

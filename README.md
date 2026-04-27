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
```Thread 1``` executes every 2 seconds and increments a counter. ```Thread 2``` executes every 1 second and stops after 5 iterations. Since both threads share the ```running``` flag, when one stops, the other stops as well. So, ```Thread 2``` will finish after ~5 seconds (5 iterations) and then ```Thread 1``` will stop too, having performed ~2–3 iterations in that time.

### Identified issues and solutions
1. Dangling Reference (critical bug): the ```Process``` function was passed by reference while being a temporary object, leading to undefined behavior. To fix it, the ```Process``` function must be passed to the ```StartThread``` by value instead of by reference.
2. Timeout Semantics: the timeout measures total runtime instead of per-loop duration. This may be intentional, in this case there is no problem.
3. Shared flag: the shared ```running``` flag between threads may be also intentional. But if the purpose is to finish both tasks independently, having a shared flag is not a good idea.
4. Lambda Capture: the lambda capture ```[&]``` may cause some problems, since it captures all variables in the function by reference. It is better to use ```[&running, Process, timeout]```, so the lambda capture explicitly captures only the required variables: ```running``` by reference to allow shared termination between threads, and ```Process``` and ```timeout``` by value to avoid dangling references and ensure thread safety.

## Python Task – Matrix Rotation
The goal is to rotate a matrix 90° clockwise in-place.

### Identified issue
The original implementation overwrites matrix values while still using them as input, corrupting the result. Additionally, the algorithm iterates over the full matrix, causing redundant writes.

### Solution
The correct approach consists of two steps: transpose the matrix and then reverse each row. This preserves data integrity and performs the rotation correctly in-place.

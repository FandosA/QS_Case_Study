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
Thread 1: executes every 2 seconds and increments a counter.
Thread 2: executes every 1 second and stops after 5 iterations.
Since both threads share the running flag, when one stops, the other stops as well.

Expected behavior:

Thread 2 finishes after ~5 seconds.
Thread 1 performs ~2–3 iterations in that time.

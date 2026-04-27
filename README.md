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
2. Timeout Semantics: the timeout measures total runtime instead of per-loop duration. This may be intentional, but the naming could be misleading and should be clarified.
3. Shared flag: the shared ```running``` flag between threads may be also intentional. If independent execution is desired, a shared flag introduces unnecessary coupling and may not be appropriate.
4. Lambda Capture: using ```[&]``` may unintentionally capture variables by reference, which is unsafe in concurrent contexts. It is better to use ```[&running, Process, timeout]```, so the lambda capture explicitly captures only the required variables: ```running``` by reference to allow shared termination between threads, and ```Process``` and ```timeout``` by value to avoid dangling references and ensure thread safety.

## Python Task – Matrix Rotation
The goal is to rotate a matrix 90° clockwise in-place.

### Identified issue
The original implementation overwrites matrix values while still using them as input, corrupting the result. Additionally, the algorithm iterates over the full matrix, causing redundant writes.

### Solution
The correct approach consists of two steps: transpose the matrix and then reverse each row. This preserves data integrity and performs the rotation correctly in-place.

## Case Study – Catching a UAV

### Goal
Design a technical approach to detect, track (in 3D), and capture a UAV using a robotic arm and a gripper with a monocular camera.

### Proposed Approach
A possible approach consists of the following steps:
1. **2D Detection**: use a deep learning-based object detector (e.g. YOLO) to detect the UAV in the image. The output will be the bounding box in pixel coordinates. The limitation of this approach is that we have no depth information.
2. **3D Tracking**: since a single camera does not provide direct depth, additional methods are required. For example: Monocular Pose Estimation (PnP) assuming known UAV geometry, Visual Servoing (IBVS/PBVS) to control the robot directly using image-space error, or temporal filtering such as Kalman filter to stabilize estimation over time.

### Challenges of Using a Single Camera
- No direct depth measurement
- Scale ambiguity
- Sensitivity to occlusions
- Motion blur in dynamic scenarios

### Mitigation Strategies
- Use known object dimensions
- Apply temporal filtering (e.g. Kalman filter)
- Use visual servoing instead of full 3D reconstruction
- Leverage motion of the robot for multi-view perception

### Alternative Sensor Approaches
To improve robustness and simplify 3D estimation:
- Stereo cameras → direct depth estimation
- RGB-D sensors → dense depth information
- LiDAR → robust distance measurement
- Sensor fusion → combining multiple modalities for improved accuracy

## Conclusion
This case study highlights key challenges in:
- Safe multithreading design
- In-place data transformations
- Real-world robotic perception under sensor constraints

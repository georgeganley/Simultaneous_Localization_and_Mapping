## Acknowledgments

This project was completed as part of the AI for Robotics course at Georgia Tech. This repository does not include the code or solution to the project, but does illustrate the outcomes of a successful Kalman filtering implementation.

## Overview

This project tackles the challenge of navigating a drone through a dense jungle to locate and extract a piece of ancient treasure. The environment is filled with unknown trees, whose positions and sizes must be inferred as the drone moves. Additionally, the drone’s own position is unknown and must be accurately tracked. 

The project is divided into two main parts:
1. **SLAM**: Estimate the locations of the drone and the trees in the jungle using measurements and motions.
2. **Navigation**: Plan a safe path towards the known treasure location and extract it without colliding with any trees.

## What is SLAM?

**SLAM (Simultaneous Localization and Mapping)** is the process by which a moving vehicle (in this case, a drone) builds a map of an unknown environment while also estimating its own location within that environment. 

- **Localization**: Determining the drone’s position and orientation (i.e., its pose) at each time step.
- **Mapping**: Building or updating the map of the environment based on sensor measurements (e.g., distances and bearings to trees).

When done jointly, SLAM enables the vehicle to navigate in an environment without prior knowledge of landmarks’ locations.

## Why SLAM Matters for This Project

1. **Unknown Environment**: The jungle is filled with trees of unknown size and location. SLAM allows us to discover these trees and track our drone in real-time.
2. **No Absolute GPS**: We rely on onboard sensors giving noisy distance and bearing measurements to local landmarks (trees).
3. **Dynamic Sensing**: Trees may enter or leave the drone’s sensor horizon, so the map must be continuously updated.
4. **Collision Avoidance and Navigation**: Accurate tree locations and drone position estimates are crucial to avoid crashes and successfully reach the treasure.

## SLAM Workflow

1. **Drone Movement (Control/Prediction Step)**  
   - The drone executes a steering command (turn) followed by a forward movement.  
   - In practice, we model this with a state transition function:
   
     $$x_{t} = f(\mathbf{x}_{t-1}, \mathbf{u}_t) + \mathbf{w}_t$$
     
	 where  
     - $(\mathbf{x}_t)$ is the drone’s state (position and orientation) at time \(t\).  
     - $(\mathbf{u}_t)$ represents the control input (steering angle, distance).  
     - $(\mathbf{w}_t)$ is noise in the motion model (often Gaussian).

2. **Measurements (Update Step)**  
   - The drone’s sensors measure the distance, bearing, and size of any tree within the sensor horizon.  
   - We update the drone and tree positions using a measurement model:
     
	 $$z_{t} = h(x_{t}, \mathbf{l}_i) + \mathbf{v}_t$$
	 
     where  
     - $(\mathbf{z}_{t})$ are the sensor measurements (distance, bearing, size).  
     - $(\mathbf{l}_i)$ represents the location of tree \(i\).  
     - $(\mathbf{v}_t)$ is measurement noise (again, often modeled as Gaussian).

3. **Map and State Update**  
   - After receiving each measurement and movement, the drone updates its belief about the state (its own position) and the positions of trees.  
   - The goal is to minimize the error in both the drone’s estimated location and the trees’ estimated locations.

## Navigation and Treasure Extraction

After building a sufficiently accurate map:
- The drone uses the mapped tree locations and its current location to **plan a path** to the treasure.
- The drone moves in increments, with each move defined by the distance (meters) and steering angle (radians).
- **Collision Detection**: Any path that intersects a tree’s canopy is considered a crash.
- **Treasure Extraction**: Once within 0.25 meters of the treasure, the drone issues an `extract` command.

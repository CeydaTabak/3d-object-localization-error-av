1. Motivation

Autonomous driving systems require accurate understanding of the surrounding environment, particularly the ability to determine the 3D positions of objects such as vehicles and pedestrians.

Traditional perception systems rely on sensors like:

LiDAR (high accuracy, high cost)
Radar (robust but limited spatial resolution)

In contrast, camera-based systems are:

Low-cost
Lightweight
Easily scalable

However, cameras do not provide direct depth information. This raises an important question:

Can a camera-only system estimate real-world object positions reliably?

2. Research Objective

This project investigates whether a camera-based perception pipeline can provide meaningful 3D localization using:

2D object detection
Monocular depth estimation

Specifically, the study evaluates:

The performance of YOLO-based detection models (YOLO26n and YOLO26s)
The effectiveness of combining detection with monocular depth estimation (MiDaS)
The feasibility of using such a system in real-time autonomous driving scenarios

3. System Overview

The proposed system combines three key components:

Object Detection

YOLO-based models are used to detect objects in image space:
Cars
Pedestrians
Each detection provides a bounding box and its center point (cx, cy).

Depth Estimation

Depth information is estimated using MiDaS, a monocular depth estimation model.
Produces relative depth from a single image
Does not directly output metric distances
Requires scaling to match real-world units
Ground Truth Reference

The nuScenes dataset provides ground truth annotations:

3D object positions [x, y, z] in real-world coordinates
Derived from LiDAR and sensor fusion
These ground truth values are used as a reference to evaluate the system.

4. Methodology

The system follows a multi-stage pipeline:

Step 1 — Detection (2D)
Apply YOLO-based models to detect objects
Extract bounding box centers (cx, cy)

Step 2 — Ground Truth Processing
Retrieve object annotations from nuScenes
Convert coordinates through transformations:
Global → Ego → Camera → Image Plane
Project 3D ground truth points into 2D image coordinates (u, v)

Step 3 — Matching
Match detected objects with ground truth using pixel proximity
Ensure class consistency (car / pedestrian)

Step 4 — Depth Estimation
Generate depth map using MiDaS
Sample depth at detection centers
Convert relative depth into metric scale using ground truth calibration

Step 5 — 3D Reconstruction
Back-project 2D image points into 3D camera space
Transform coordinates into the ego (vehicle) frame

Step 6 — Localization Error Computation
Compare predicted 3D positions with ground truth
Evaluate error in real-world units (meters)

5. Evaluation Metrics
Localization Error

The primary metric is the spatial error between predicted and ground truth positions:

error = || (x_pred, y_pred) - (x_gt, y_gt) ||

This represents the distance between predicted and actual object location in meters.

Additional Metrics
Number of detections
Number of matched objects
Mean / median localization error
Processing time per frame
Frames per second (FPS)

6. Dataset

nuScenes v1.0-mini
-10 scenes
-390 front camera images

The dataset provides:

Multi-sensor recordings
High-quality 3D object annotations
Real-world driving scenarios

7. Real-Time Considerations

The system is evaluated not only in terms of accuracy but also runtime performance.

Since autonomous driving systems require low latency:

Inference time per frame is measured
FPS is computed to assess real-time feasibility

This allows analyzing the trade-off between:

Model complexity
Localization accuracy 


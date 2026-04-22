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

## 7. Experimental Outputs

The project generates the following outputs:

- overall model comparison
- scene-level summaries
- per-object localization results
- class-wise comparison

The main summary is shown below.

### Overall Comparison

| Model | Scenes | Images | Total Matches | Avg Matches / Image | Mean XY Error (m) | Median XY Error (m) | Mean Total Time (s) | FPS |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| YOLO26n | 10 | 404 | 1202 | 2.97 | 12.44 | 5.38 | 0.0408 | 24.57 |
| YOLO26s | 10 | 404 | 1520 | 3.76 | 14.67 | 7.05 | 0.0423 | 23.62 |

### Class-wise Comparison

| Model | Class | Matches | Mean XY Error (m) | Median XY Error (m) |
|---|---|---:|---:|---:|
| YOLO26n | car | 940 | 18.88 | 12.41 |
| YOLO26n | pedestrian | 262 | 10.37 | 3.70 |
| YOLO26s | car | 1159 | 20.03 | 13.09 |
| YOLO26s | pedestrian | 361 | 12.30 | 5.21 |

## 8. Preliminary Interpretation

The results indicate that YOLO26s produces more matched detections, while YOLO26n achieves lower localization error and slightly higher runtime efficiency. This suggests that, in a camera-only 3D estimation pipeline, improved detection coverage does not necessarily lead to better real-world spatial accuracy.

## 9. Repository Contents

- `3d_localization_pipeline.ipynb` : main experiment notebook  
- `all_scenes_overall_comparison.csv` : final model comparison  
- `all_scenes_summary.csv` : scene-level summaries  
- `all_scenes_object_results.csv` : per-object localization results  
- `all_scenes_classwise_comparison.csv` : class-based comparison  

## 10. Notes

- The nuScenes dataset is not uploaded due to size limitations.
- MiDaS produces relative depth and requires scale conversion.
- This repository focuses on evaluation of a camera-based 3D localization pipeline.


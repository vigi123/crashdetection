# CRASH DETECTION PIPELINE (EventSeeker) 

> **Automated 3D anomaly detection for long-duration surveillance footage.**

## 💡 The Problem

In surveillance and forensic analysis, reviewing footage is a bottleneck. When an event like a car crash occurs within a **24-48 hour window**, human operators must manually scrub through clips to find the exact moment.

Furthermore, standard 2D computer vision struggles with **perspective distortion**. Traditional 2D bounding boxes cannot distinguish between two cars actually colliding versus two cars simply overlapping in the camera's line of sight (e.g., one passing safely behind the other).

## 🚀 Our Solution

**EventSeeker** is an ML-based pipeline that ingests long video files, translates 2D scenes into 3D space, and automatically generates a text file containing precise timestamps of probable incidents.

Instead of generic motion detection or flawed 2D overlap checks, we use a 3D physics-based approach:

1. **Object Recognition:** A trained **YOLO** model annotates objects (cars, people, vehicles) in every frame.
2. **Depth Estimation (New):** We integrate the **MiDaS** model to generate a depth map for each frame, extracting the Z-axis (depth) value for every detected object.
3. **3D Velocity Vector Analysis:** The model calculates the 3D velocity vectors (X, Y, and Z planes) for identified objects across sequential frames.
4. **Collision Prediction:** By analyzing the magnitude and direction of these 3D vectors, the system predicts if objects are physically converging (intersecting paths in 3D space) at high speeds, effectively filtering out false positives caused by 2D perspective overlap.
5. **Logging:** If a genuine 3D convergence is detected, the timestamp and object IDs are logged to a text file for immediate human review.

## 🚧 Current Status:

**Current Capabilities:**

* ✅ **Input:** Accepts 2D video files (mp4, avi) and a text prompt (defining the anomaly type).
* ✅ **Spatial Awareness:** Converts standard 2D footage into 3D spatial data using monocular depth estimation.
* ✅ **Detection Logic:** Specialized for **Car Crashes**, using Z-axis data to distinguish actual impacts from optical illusions/near-misses.
* ✅ **Output:** Generates a `.txt` log with timestamps of potential crashes.

**Future Goals:**

* Expand the "Text Input" feature to dynamically switch detection logic for other anomalies (e.g., "fights," "murders," "bike accidents") based on the user's prompt.
* Optimize the MiDaS inference pipeline to run closer to real-time on edge devices.

## ⚙️ How It Works (Under the Hood)

1. **Input:** The user provides a raw video file and a text string (e.g., "find car crash").
2. **Processing:**
* The YOLO model scans the video frame-by-frame to isolate bounding boxes.
* Simultaneously, MiDaS computes a relative depth map of the scene.
* Centroids of YOLO bounding boxes are mapped against the MiDaS depth map to extract `(x, y, z)` coordinates for each vehicle.
* A custom algorithm tracks these coordinates over time to determine 3D speed and trajectory.
* The logic triggers a "Convergence Event" when two distinct object vectors rapidly intersect at the same physical point in space at the same timestamp.


3. **Output:**
* The system outputs a `report.txt` file listing the specific timestamps where the logic triggered.
* *Example Output:* `Possible collision detected at 04:23:15 between Object_1 (Car) and Object_2 (Car) at relative depth Z: 45.`



## 🛠️ Tech Stack

* **Language:** Python
* **Computer Vision:** OpenCV (`cv2`)
* **Object Detection:** YOLO (You Only Look Once)
* **Depth Estimation:** MiDaS (Monocular Depth Estimation) / PyTorch
* **Math/Physics:** NumPy (for 3D vector calculation and coordinate mapping)

## NOTE

This is a hackathon project, so things might be messy! If you have ideas for optimizing the 3D vector convergence logic or adding new anomaly classes, feel free to open a PR.

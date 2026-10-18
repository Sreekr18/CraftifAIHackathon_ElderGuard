# CraftifAIHackathon_ElderGuard
lderGuard is a real-time edge vision pipeline that detects elderly falls using TensorRT FP16 pose estimation (yolov8_pose_parallel), verifies prolonged immobility, and triggers MQTT alerts while applying on-device Gaussian blurring before saving any frames.   

# ElderGuard: Privacy-Preserving Vision-Based Fall Detection Pipeline

#Screenshot of Pipegen running
<img width="1855" height="1049" alt="Screenshot from 2026-10-18 18-45-31" src="https://github.com/user-attachments/assets/c58da368-59be-4f43-b351-e50ad9606fd2" />
<img width="1855" height="1049" alt="Screenshot from 2026-10-18 18-45-49" src="https://github.com/user-attachments/assets/ede536c3-8717-4765-9066-134a786f5a51" />

<img width="1857" height="1058" alt="Screenshot from 2026-10-18 18-13-39" src="https://github.com/user-attachments/assets/4099e4ee-5109-4c6e-ba57-8051d768dd4f" />
<img width="1857" height="1058" alt="Screenshot from 2026-10-18 18-13-23" src="https://github.com/user-attachments/assets/860acbcf-3f87-49dc-9a4e-82da034db765" />
<img width="1730" height="966" alt="Screenshot from 2026-10-18 18-03-28" src="https://github.com/user-attachments/assets/5e3ea2d5-fd4b-4f5a-8c0f-c7bde52ff106" />
<img width="1909" height="1052" alt="Screenshot from 2026-10-18 17-58-56" src="https://github.com/user-attachments/assets/064fe8b9-e539-4f0b-bb55-a4612298fcc1" />
<img width="1846" height="1049" alt="Screenshot from 2026-10-18 18-10-30" src="https://github.com/user-attachments/assets/98722b8e-c93b-4da2-ad45-ebdef289cd8e" />





**Track:** Track A — PipeGen (Perception) | Track 2: Fall Detection and Elder Care[cite: 3]  
**Platform:** CraftifAI PipeGen v0.5.1 on NVIDIA GPU / DeepStream[cite: 3]

---

## 1. Problem Statement
Accidental falls among elderly individuals at home frequently go unnoticed for hours, resulting in severe physical complications, dehydration, and delayed emergency response[cite: 3]. Conventional video monitoring intrudes on privacy within personal living spaces[cite: 1, 3], while wearable alarms and panic buttons are often forgotten or inaccessible during a sudden fall[cite: 3].

---

## 2. Solution Overview
ElderGuard is an edge-accelerated perception pipeline that continuously monitors room activity using real-time human pose estimation, verifies sustained immobility through a temporal finite state machine (FSM), and applies on-device privacy blurring over faces and surroundings before saving alert artifacts or publishing telemetry[cite: 1, 3, 5].

---

## 3. Evidence

### Pipeline IR Node Graph
![Pipeline IR Node Graph](assets/pipeline_ir_graph.png)

### TensorRT Speed & Latency Report
![TensorRT Speed Report](assets/tensorrt_speed_report.png)

---

## 4. Technical Specifications

- **Input:** Single camera feed via USB Camera (`/dev/video0`) or video file, preprocessed to 640×640 at FP16 precision[cite: 2, 3, 5, 6].
- **Tasks in Order:**
  1. *Video Ingestion & Preprocessing:* Frame decoding, scaling, and FP16 normalization[cite: 5].
  2. *2D Skeleton / Keypoint Detection:* Extracting 17 COCO keypoints and bounding boxes per person[cite: 5, 7].
  3. *Person Tracking:* Frame-by-frame tracking to evaluate descent speed and motion trajectory[cite: 5].
  4. *Fall & Immobility FSM:* Detects rapid descent, verifies torso angle relative to floor horizontal axis (< 30°) and bounding-box ratio ($W/H > 1.2$), and requires continuous immobility for 10 seconds before confirming an alarm[cite: 5].
  5. *Privacy Redaction:* Applies Gaussian blur (radius 51) dynamically over faces and background before capturing frames, rendering a skeletal stick-figure overlay[cite: 1, 2, 5].
  6. *Alert & Event Sink:* Emits low-latency alerts and event logs[cite: 1, 3].
- **Model:** `yolov8_pose_parallel` (YOLOv8s-Pose)[cite: 5, 7].
- **Precision:** TensorRT FP16 engine[cite: 3, 6].
- **Outputs:**
  - Real-time display overlay[cite: 3].
  - MQTT event telemetry published to `eldercare/alerts/fall`[cite: 1, 3].
  - Local JSON metadata event log[cite: 1, 3].
  - Privacy-masked keyframe snapshots[cite: 1, 2, 5].

---

## 5. Repository Structure

```text
├── assets/
│   ├── pipeline_ir_graph.png      # Pipeline IR graph screenshot
│   └── tensorrt_speed_report.png  # TensorRT benchmark speed report screenshot
├── export/                        # PipeGen exported pipeline files
│   ├── config.txt                 # DeepStream / pipeline configuration
│   └── run.sh

# Vision-Driven Autonomous 4WD Mobile Robot 🏎️👁️

## 🛠️ Project Specifications
* **Project Nature:** Completed Mobile Robotics Build
* **Core Domains:** Computer Vision (CV), Differential Drive Kinematics, Image Processing, Embedded Systems
* **Status:** Fully Functional / Operational

---

## 📌 Technical Overview & Control Architecture
This repository hosts the source code, hardware integration metrics, and vision processing algorithms for an autonomous 4WD mobile vehicle. While traditional line-following robots rely on simple, localized infrared (IR) sensor arrays that pass binary high/low values to a microcontroller, this platform elevates the control loop by utilizing an onboard **Machine Vision system**. 

By processing high-dimensional image frames in real time, the system extracts path geometries, calculates heading errors dynamically, and maps those errors to a 4WD differential drive system to maintain smooth, high-speed line tracking under variable ambient lighting conditions.

### 🎯 Key Engineering Achievements
* **Computer Vision Pipeline:** Engineered a low-latency image processing pipeline utilizing color masking, region-of-interest (ROI) filtering, and edge/centroid tracking to pinpoint the target navigation line.
* **4WD Differential Kinematics:** Developed a kinematic controller that translates pixel-offset heading errors into real-time PWM velocity commands for the left and right motor pairs.
* **Ambient Lighting Adaptation:** Configured dynamic thresholding matrix parameters to isolate path lines and prevent tracking failure caused by glare, shadow casting, or environmental noise.

---

## 📂 Repository Architecture
* **`/src`**: Houses the core OpenCV/machine vision tracking scripts, image processing routines, and motor execution loops (C++/Python).
* **`/hardware`**: Contains the physical chassis layout, motor driver interface schematics, power distribution parameters, and camera mount geometries.
* **`/docs`**: Contains geometric calibration steps, speed benchmarking sheets, and operational manuals.

---

## 📋 Project Development & Validation Milestones
- [x] Chassis structural selection and 4WD high-torque motor mounting
- [x] Motor driver integration and basic forward/reverse differential drive calibration
- [x] Camera sensor interfacing and optimization of the onboard hardware processing frame rate
- [x] Deployment of the vision script (Color masking and center-of-mass centroid calculation)
- [x] Closed-loop integration: Mapping pixel heading error to proportional motor speed adjustment
- [x] Dynamic field testing under changing light conditions and challenging track curves
- [x] Full deployment of a highly responsive, vision-guided autonomous tracking platform

---

> *Note: Code structures and parameter matrices are completely open-source within their respective directories for testing and development.*

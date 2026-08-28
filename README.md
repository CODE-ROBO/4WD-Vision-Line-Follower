<h1 align="center">Vision-Driven Autonomous 4WD Mobile Robot 🏎️👁️</h1>
<h4 align="center">Real-Time Computer Vision, Differential Kinematics, & Embedded Control</h4>

<p align="center">
  <br>
  <img src="https://img.shields.io/badge/OpenCV-00FFFF?style=for-the-badge&logo=opencv&logoColor=black" alt="OpenCV"/>
  <img src="https://img.shields.io/badge/Python-FFD700?style=for-the-badge&logo=python&logoColor=black" alt="Python"/>
  <img src="https://img.shields.io/badge/C++-00599C?style=for-the-badge&logo=c%2B%2B&logoColor=white" alt="C++"/>
  <img src="https://img.shields.io/badge/Fusion360-00FFFF?style=for-the-badge&logo=Fusion360&logoColor=black" alt="Fusion360"/>
  <img src="https://img.shields.io/badge/3D Printing-00GGGG?style=for-the-badge&logo=3DPrinting&logoColor=black" alt="3D Printing"/>
  <img src="https://img.shields.io/badge/3D Printing-00GGGG?style=for-the-badge&logo=3DPrinting&logoColor=black" alt="3D Printing"
</p>

<p align="center">
  <img src="https://via.placeholder.com/800x400/0a0a0a/00FFFF?text=[INSERT+ROBOT+TRACKING+GIF+OR+HARDWARE+RENDER+HERE]" alt="Autonomous 4WD Robot Render" width="100%"/>
</p>

---

<details open>
  <summary><b>📑 DIRECTORY TERMINAL (TABLE OF CONTENTS)</b></summary>
  <ol>
    <li><a href="#overview">Executive System Overview</a></li>
    <li><a href="#datasheet">System Datasheet & Engineering Targets</a></li>
    <li><a href="#logic">Computer Vision & Kinematic Control Logic</a></li>
    <li><a href="#hardware">Hardware Fabrication & Bill of Materials</a></li>
    <li><a href="#pipeline">Vision Processing & Execution Pipeline</a></li>
    <li><a href="#architecture">Repository Architecture & CI/CD</a></li>
    <li><a href="#validation">Empirical Validation Matrix (Development Log)</a></li>
    <li><a href="#deployment">Deployment & Reproducibility</a></li>
    <li><a href="#team">R&D Framework & Development Scope</a></li>
    <li><a href="#academic">Academic Trajectory</a></li>
    <li><a href="#citation">Academic Citation</a></li>
  </ol>
</details>

---

### <a id="overview"></a>🌐 EXECUTIVE SYSTEM OVERVIEW

<div align="justify">
This repository hosts the source code, hardware integration metrics, and vision processing algorithms for an autonomous 4WD mobile vehicle. While traditional line-following robots rely on simple, localized infrared (IR) sensor arrays that pass binary high/low values to a microcontroller, this platform elevates the control loop by utilizing a sophisticated onboard <b>Machine Vision system</b>. 

By processing high-dimensional image frames in real-time, the system extracts path geometries, dynamically calculates heading errors, and maps those errors directly to a 4WD differential drive system. This architecture ensures smooth, high-speed tracking and robust trajectory correction under variable ambient lighting conditions, preventing the track-loss common in IR-based systems.
</div>

---

### <a id="datasheet"></a>📋 SYSTEM DATASHEET & ENGINEERING TARGETS

<div align="justify">
The architecture bridges embedded image processing with deterministic mechatronic actuation to maintain a continuous, low-latency control loop.
</div>

| Subsystem | Specification | Engineering Objective |
| :--- | :--- | :--- |
| **Control Architecture** | Embedded SBC (Single Board Computer) | Execute multi-threaded vision and PWM pipelines simultaneously. |
| **Vision Framework** | OpenCV Image Processing Pipeline | High-speed contour extraction and centroid localization. |
| **Drive Kinematics** | 4-Wheel Differential Steering | Translate angular correction to left/right wheel RPM variables. |
| **Environmental Adaptation**| Dynamic Thresholding Matrix | Isolate target paths despite glare, shadows, or track noise. |
| **Response Latency** | Optimized Frame Rate Processing | Ensure mechanical actuation occurs before geometric tracking loss. |
| **Project Status** | **OPERATIONAL** | Fully functional and field-validated under dynamic track conditions. |

---

### <a id="logic"></a>🧠 COMPUTER VISION & KINEMATIC CONTROL LOGIC

<div align="justify">
To achieve high-speed path tracking, the vision script extracts the center of mass (centroid) of the target line within a defined Region of Interest (ROI). Let $(c_x, c_y)$ represent the target's centroid, and $w_{frame}$ represent the pixel width of the camera frame. The lateral heading error $e(t)$ is calculated as the offset from the center of the camera's field of view:
</div>

$$e(t) = c_x - \frac{w_{frame}}{2}$$

<div align="justify">
A Proportional-Integral-Derivative (PID) controller evaluates this pixel error to generate a dynamic angular velocity correction factor ($\omega$):
</div>

$$\omega = K_p e(t) + K_i \int e(t)dt + K_d \frac{de(t)}{dt}$$

<div align="justify">
Using Differential Drive Kinematics, the target velocities for the left wheel array ($v_L$) and right wheel array ($v_R$) are derived using the base linear velocity ($V_{base}$) and the chassis track width ($L$). The motor drivers receive these values as scaled PWM signals:
</div>

$$v_L = V_{base} - \frac{\omega \cdot L}{2}$$

$$v_R = V_{base} + \frac{\omega \cdot L}{2}$$

---

### <a id="hardware"></a>⚙️ HARDWARE FABRICATION & BILL OF MATERIALS

<div align="justify">
The physical architecture was designed to handle high-torque differential slipping and provide a vibration-damped mount for the optical sensor to prevent motion blur during high-speed maneuvering.
</div>

* 🛡️ **Chassis Dynamics:** Rigid 4WD frame layout ensuring all four wheels maintain uniform surface contact during sharp differential turns.
* 👁️ **Optical Mounting:** Angled camera geometry calibrated to capture an optimal Region of Interest (ROI) slightly ahead of the chassis for predictive tracking.
* ⚡ **Power Distribution:** Dual-rail power management isolating logic-level voltage (SBC/Sensors) from high-current motor spikes to prevent brownouts.

**Primary Hardware Bill of Materials (BOM):**
| Component | Material / Specification | Subsystem |
| :--- | :--- | :--- |
| **Master Controller** | Embedded SBC (e.g., Raspberry Pi / Jetson) | Logic Engine & Vision Processing |
| **Optical Sensor** | High-Framerate Telemetry Camera | Machine Vision Input |
| **Motor Drivers** | High-Current Dual H-Bridge | PWM Signal Amplification |
| **Actuators** | 4x High-Torque DC Gear Motors | Physical Locomotion |
| **Chassis** | Aluminum/Acrylic 4WD Frame | Structural Containment |

---

### <a id="pipeline"></a>📡 VISION PROCESSING & EXECUTION PIPELINE

<div align="justify">
The software architecture relies on a highly optimized, continuous loop running at the maximum achievable frame rate of the embedded hardware.
</div>

1. **Frame Ingestion:** The camera captures RGB frames and crops them to a strict ROI, ignoring background noise and reducing computational overhead.
2. **Color Masking & Binarization:** Frames are converted to the HSV color space, and dynamic thresholding is applied to isolate the path geometry into a stark binary mask.
3. **Centroid Extraction:** OpenCV moments are calculated to find the $(c_x, c_y)$ coordinates of the target path.
4. **Kinematic Execution:** The PID loop converts the $c_x$ offset into proportional PWM adjustments, triggering the motor drivers via GPIO pins.

---

### <a id="architecture"></a>🗄️ REPOSITORY ARCHITECTURE & CI/CD

<div align="justify">
<i>Structured for absolute transparency, bridging embedded computer vision software with mechatronic hardware schematics.</i>
</div>

```text
📁 Vision-Autonomous-4WD/
│
├── 📁 .github/workflows/     # CI/CD: Automated linting for Python/C++ vision scripts
├── 📁 src/                   # Core Logic & Algorithms
│   ├── vision_tracker.py     # OpenCV color masking and centroid extraction pipeline
│   ├── kinematics_pid.py     # Differential drive math and PWM mapping
│   └── main_loop.py          # Multi-threaded execution script
│
├── 📁 hardware/              # Physical Build Assets
│   ├── wiring_schematic.pdf  # Motor driver, SBC, and power rail routing
│   └── CAD_models/           # Chassis layouts and camera mount geometries (STEP/STL)
│
├── 📁 docs/                  # System documentation
│   ├── dynamic_thresholds.csv # Calibration logs for varying ambient lighting conditions
│   └── PID_tuning_log.md     # Kp, Ki, Kd parameter adjustments for speed benchmarking
│
├── requirements.txt          # Python dependencies (OpenCV, NumPy, GPIO libraries)
└── README.md                 # Main system dossier

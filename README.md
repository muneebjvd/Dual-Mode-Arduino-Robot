# Dual-Mode Line Following & Obstacle Avoiding Robot

An autonomous robotic rover built on the Arduino platform capable of dynamically switching between two distinct operational states: precise line-following and intelligent obstacle avoidance. The system utilizes a push-button interrupt to toggle modes on the fly, making it a versatile platform for exploring sensor fusion, motor control, and state-machine programming.

---

## 🚀 Key System Features
* **Dual-Mode Operation:** Seamlessly transitions between Line-Following mode and Obstacle-Avoiding mode via a physical push-button hardware toggle.
* **Active Environment Scanning:** In obstacle-avoidance mode, the robot uses a servo-mounted ultrasonic sensor to "look" left and right, actively determining the clearest path to navigate around blockages.
* **Precision Tracking:** Utilizes dual Infrared (IR) sensors to maintain high-speed adherence to complex, high-contrast path layouts.
* **Robust Motor Control:** Employs an L298N dual H-bridge motor driver to deliver independent, differential steering and speed control to a 4-wheel drive (4WD) chassis.

---

## 🛠️ Hardware Components

| Component | Description | System Function |
| :--- | :--- | :--- |
| **Arduino Uno / Mega** | Microcontroller | The central processing unit executing the C++ control logic. |
| **L298N Motor Driver** | Dual H-Bridge | Controls the direction and PWM speed of the DC motors. |
| **HC-SR04 Ultrasonic** | Time-of-Flight Sensor | Emits high-frequency sound waves to measure distance to frontal objects. |
| **Micro Servo Motor** | Positional Actuator | Sweeps the ultrasonic sensor 180 degrees to scan for clear paths. |
| **IR Sensor Modules (x2)** | Reflectance Sensors | Detects the contrast between the dark line and light floor. |
| **Push Button** | Momentary Switch | Acts as a hardware interrupt/toggle to switch between operational modes. |
| **4WD Car Chassis** | Mechanical Frame | Houses all electronics and provides mobility via 4 geared DC motors. |

---

## 🧠 Software Architecture & Logic Flow

The robot's firmware is designed around a state-based execution loop managed within the Arduino IDE. 

### 1. Mode Selection Logic
A debounce-protected push-button routine continuously polls for user input. 
* **State 0 (Line Following):** The robot strictly reads the left and right IR sensors. If the left sensor detects a line, the rover turns left; if the right detects a line, it turns right.
* **State 1 (Obstacle Avoidance):** The IR sensors are ignored. The robot moves forward until the HC-SR04 detects an object within a critical threshold (e.g., < 15cm). 

### 2. Obstacle Resolution Routine
When an object is detected in State 1:
1. The robot **halts** all forward movement.
2. The servo motor rotates the ultrasonic sensor to **0° (Right)** and takes a distance reading.
3. The servo motor rotates the ultrasonic sensor to **180° (Left)** and takes a distance reading.
4. The microcontroller compares the two distances. The robot executes a pivot turn toward the direction with the **greatest clearance** and resumes forward motion.

---

## 🔌 Conceptual Wiring Map

```text
[ Arduino Core ]
       │
       ├─► (Digital Pins) ──► [ L298N Motor Driver ] ──► [ 4x DC Motors ]
       │
       ├─► (PWM Pin) ───────► [ Micro Servo Motor ]
       │                             │
       ├─► (Trigger/Echo) ──► [ HC-SR04 Ultrasonic Sensor ] (Mounted on Servo)
       │
       ├─► (Digital Read) ──► [ Left IR Sensor ]
       │
       ├─► (Digital Read) ──► [ Right IR Sensor ]
       │
       └─► (Digital Read) ──► [ Mode Toggle Push Button ]

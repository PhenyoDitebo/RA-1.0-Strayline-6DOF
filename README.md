# RA-1.0: Strayline - Wireless Gesture Controlled 6DOF Robotic Arm

A high-level mechatronics system designed for wireless human-gesture mimicry. This project features a 6-degree-of-freedom (6DOF) robotic arm controlled via a custom-built wearable glove that transmits motion data using a Master-Slave Bluetooth configuration.

---

## Table of Contents
- Overview
- Features
- System Architecture
- Hardware Requirements
- Electrical Subsystems
- Software & Logic
- Technical Specifications
- Future Development

---

## Overview
The **RA-1.0: Strayline** was developed to bridge the gap between human motion and mechanical actuation. By utilizing an IMU (Inertial Measurement Unit) and flex sensors on a wearable glove, the system maps real-time human gestures to the robotic arm's joints.

**Key Characteristics:**
- **Arm Type:** 6DOF (Base, Shoulder, Elbow, Wrist, Gripper).
- **Control:** Wireless Bluetooth (HC-05).
- **Fabrication:** 3D-printed PLA components.
- **Power:** Consolidated DC-DC Buck Converter architecture for high-current stability.

---

## Features
- **Wireless Gesture Mapping:** Remote operation using a wearable "Master" glove.
- **Precision Filtering:** Integrated "Dead-Zone" logic to eliminate servo jitter and noise.
- **High-Torque Actuation:** Utilizes DS3240MG and MG996R servos for heavy-lifting capabilities.
- **Smooth PWM Control:** PCA9685 driver integration for 16-channel high-resolution movement.
- **Robust Power Management:** Transitioned from unstable LiPo power to a stabilized 20W buck converter system.

---

## System Architecture

```text
┌──────────────────┐      Bluetooth      ┌──────────────────┐      Serial PWM     ┌────────────────┐
│  Wearable Glove  │ ◄─────────────────► │   Arduino Uno    │ ◄────────────────►  │  Robotic Arm   │
│ (Master Sender)  │       (HC-05)       │ (Slave Receiver) │      (PCA9685)      │  (6x Actuators)│
└──────────────────┘                     └──────────────────┘                     └────────────────┘
         │                                         │                                       │
         ├─ Arduino Nano                           ├─ Signal Parsing                       ├─ 2x DS3240MG
         ├─ MPU-6050 (Tilt)                        ├─ Dead-Zone Logic                      ├─ 4x MG 996R
         └─ 3x Flex Sensors                        └─ Power Regulation                     └─ 1x Nema 17


```
## Hardware Requirements

### Microcontrollers & Wireless Communication
* **Arduino Uno:** Acts as the primary receiver (Slave) node at the robotic arm base.
* **Arduino Nano:** A compact microcontroller integrated into the wearable glove (Master).
* **2x HC-05 Bluetooth Modules:** Configured in a Master-Slave pair to establish a dedicated wireless serial link.

### Actuators & Drivers
* **2x DS3240MG Servos:** High-torque metal gear servos utilized for the primary load-bearing shoulder joints.
* **4x MG 996R Servos:** Standard digital servos used for the elbow, wrist, and gripper mechanisms.
* **1x Nema 17 Stepper Motor:** Provides high-precision Z-axis rotation for the arm's base.
* **PCA9685 PWM Driver:** A 16-channel, 12-bit I2C bus driver used to offload PWM timing from the Arduino for smoother servo movement.
* **TB6600 Stepper Driver:** A professional-grade driver used to manage the current and micro-stepping of the Nema 17 base motor.

### Sensors
* **MPU-6050 (IMU):** A 6-axis Gyroscope and Accelerometer used to detect hand tilt and wrist orientation.
* **Flex Sensors:** Variable resistance sensors mounted on the glove to detect finger bending for gripper control.

---

## Electrical Subsystems

### Subsystem 1: Signal Transmission
The wearable glove reads analog signals from the flex sensors and I2C data from the MPU-6050. This data is processed by the Arduino Nano and transmitted via the HC-05 Master module.

### Subsystem 2: Integrated Power (Final Version)
To eliminate the voltage drops and "brownouts" caused by high-torque servos:
* **DC-DC Buck Converter (DFR0379):** A 20W regulator that converts a 12V input down to a stable 5.5V–6V.
* **Consolidated Power:** This setup provides sufficient current (up to 4A peak) to power all servos and the stepper motor simultaneously without resetting the microcontroller.

---

## Software & Logic

### Dead-Zone Integration
To solve the issue of "servo jitter" caused by raw sensor fluctuations, the system utilizes a **Dead-Zone** algorithm.



* **Logic:** The software ignores sensor changes smaller than a predefined threshold. 
* **Result:** The arm remains perfectly still unless the human operator makes a deliberate movement, significantly reducing mechanical wear and heat buildup in the servos.

---

## Technical Specifications

| Feature | Specification |
| :--- | :--- |
| **Control Architecture** | Master-Slave Bluetooth Serial |
| **Degrees of Freedom** | 6 (5 Gesture-mapped + 1 Base Rotation) |
| **Power Input** | 12V DC (via AC-DC Adapter) |
| **Operating Voltage** | 5V - 6V (Regulated) |
| **Communication Latency** | ~0.5 Seconds |
| **Structural Material** | 3D Printed PLA |

---

## Future Development

The RA-1.0 serves as a foundation for more advanced iterations. Future updates will focus on:

1.  **Modular Actuators:** Transitioning from hobby servos to DC motors or steppers equipped with **optical encoders** for closed-loop positional feedback.
2.  **PID Control:** Implementing Proportional-Integral-Derivative logic to allow for smooth acceleration and deceleration (ramping) of arm movements.
3.  **Mechanical Reinforcement:** Redesigning the base gear assembly to utilize higher-strength materials to prevent tooth deformation under high torque.
4.  **Advanced Feedback:** Integrating force-sensitive resistors (FSRs) on the gripper to provide haptic feedback to the user's hand.


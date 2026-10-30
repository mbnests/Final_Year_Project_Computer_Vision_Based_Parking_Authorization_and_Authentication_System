# FYP: Computer Vision Based Parking Authorization and Authentication System

An automated parking management system combining **RFID access control**, **Automatic Number Plate Recognition (ANPR)** using EasyOCR and OpenCV, and an **anti-passback algorithm** to monitor and secure vehicle entry and exit.

The system uses a Python backend with an SQLite database and a computer vision pipeline. It communicates through serial communication with an Arduino microcontroller, which controls RFID readers, a motorized gate servo, a camera positioning servo, an LCD status display, and an emergency keypad override.

---

## Features

- **Dual Authentication:** Combines RFID card/tag validation with computer vision-based license plate recognition to verify both the user and the vehicle.
- **Anti-Passback Tracking:** Uses an `is_inside` status flag to prevent repeated entries or exits using the same credentials without following the correct entry/exit sequence.
- **Computer Vision and Digital Zoom:** Uses OpenCV and EasyOCR with digital zoom and adaptive thresholding to improve license plate image quality and text recognition.
- **Automatic Camera Positioning:** A servo-driven camera bracket changes the camera direction depending on whether the entry or exit RFID reader is triggered.
- **Emergency Keypad Override:** Allows administrators to activate an emergency gate override using a secure code entered through an Arduino-connected matrix keypad.
- **Local Database and Access Logging:** Stores RFID credentials, vehicle registration numbers, timestamps, access attempts, and authentication violations in a local SQLite database.

---

## System Architecture

The system consists of two main parts:

1. **Python Backend:** Handles RFID event processing, camera capture, image preprocessing, license plate recognition, authentication, and database operations.
2. **Arduino Microcontroller:** Manages RFID readers, servo motors, LCD messages, and keypad input.

The Python backend and Arduino communicate through a USB serial connection.

---

## Hardware Components

| Component | Description |
|---|---|
| Arduino | Main microcontroller |
| 2 × MFRC522 RFID Readers | Outer entry and inner exit authentication |
| RFID Cards/Tags | User identification |
| Camera | Vehicle number plate capture |
| Gate Control Servo | Controls the parking barrier |
| Camera Positioning Servo | Changes camera viewing direction |
| 16 × 2 I2C LCD | Displays system status and authentication messages |
| 4 × 4 Matrix Keypad | Emergency override code input |

### Arduino Pin Configuration

| Component | Arduino Pin / Configuration |
|---|---|
| Outer Entry RFID Reader | SS/SDA: Pin 10 |
| Inner Exit RFID Reader | SS/SDA: Pin 2 |
| RFID Shared Reset | Pin 9 |
| Gate Control Servo | A0 |
| Camera Positioning Servo | A3 |
| LCD | I2C Address: `0x27` |
| Keypad Rows | 8, 7, 6, 5 |
| Keypad Columns | 4, 3, A1, A2 |

---

## Software Stack

### Python Backend

| Library / Module | Purpose |
|---|---|
| Python 3 | Main backend programming language |
| OpenCV (`opencv-python`) | Camera capture, digital zoom, and image preprocessing |
| EasyOCR | License plate text extraction |
| NumPy | Numerical operations and image-array handling |
| PySerial (`pyserial`) | Serial communication between Python and Arduino |
| SQLite3 | Local database storage and access tracking |
| `re` | Text cleaning and processing |
| `os` | Operating-system and file operations |
| `ssl` | Security-related functions |
| `time` | Timing and performance measurements |
| `datetime` | Date and timestamp management |

### Arduino Software

**Programming Language:** C++

**Development Environment:** Arduino IDE

| Library | Purpose |
|---|---|
| MFRC522 | RFID reader communication |
| LiquidCrystal_I2C | LCD display control |
| Servo | Servo motor control |
| Keypad | Matrix keypad input handling |

---

## Computer Vision Pipeline

The system uses OpenCV and EasyOCR to identify vehicle registration numbers.

The image processing workflow includes:

1. Camera positioning based on the triggered RFID reader.
2. Waiting for the camera to settle and clearing old video frames.
3. Digital zoom to improve the license plate view.
4. Conversion of the captured image to grayscale.
5. Noise reduction using a bilateral filter.
6. Adaptive thresholding to improve character visibility.
7. License plate text detection and recognition using EasyOCR.
8. Cleaning and validation of OCR output before database comparison.

---

## Authentication Workflow

1. A user scans an RFID card at the entry or exit reader.
2. Arduino sends the RFID ID and gate direction to the Python backend.
3. The camera positioning servo adjusts the camera toward the relevant lane.
4. The camera captures the vehicle's license plate.
5. OpenCV preprocesses the image, and EasyOCR extracts the registration number.
6. Python compares the recognized plate with the registered vehicle number in SQLite.
7. The backend checks the user's current parking status using the anti-passback algorithm.
8. If authentication succeeds, the Arduino receives the command to operate the gate servo.
9. The database records the access event and updates the vehicle's inside/outside status.

If authentication fails, access is denied and the attempt is recorded.

---

## Database and Security

The system uses a local SQLite database to maintain:

- Registered RFID card IDs.
- Associated vehicle registration numbers.
- Current parking status (`is_inside`).
- Successful entry and exit records.
- RFID and number plate mismatches.
- Anti-passback violations.
- Emergency override events.
- Date and time of access attempts.

The anti-passback mechanism ensures that a registered vehicle cannot enter again before its previous exit has been recorded.

---

## Emergency Override

An Arduino-connected 4 × 4 matrix keypad provides an emergency override mechanism. Administrators can enter a configured security code to activate the gate override. The event is also recorded in the database for later review.

---

## Project Objectives

- Improve parking security through two-factor vehicle and user authentication.
- Reduce unauthorised vehicle entry through RFID and number plate matching.
- Prevent repeated or invalid entry/exit operations using anti-passback logic.
- Automate camera positioning and parking barrier operation.
- Maintain a local record of parking activities and authentication events.
- Develop a practical, low-cost parking authorization solution.

---

## Technologies Used

**Languages:** Python, C++

**Computer Vision:** OpenCV, EasyOCR

**Database:** SQLite

**Embedded Systems:** Arduino

**Communication:** USB Serial Communication

**Hardware Interfaces:** SPI, I2C, PWM

---

## Future Improvements

- Introduce a shared database for managing multiple parking gates.
- Add traffic-pattern analysis using historical access logs.
- Deploy the Python backend on a compact edge computing device.
- Integrate vehicle detection to trigger OCR only when a vehicle is present.

---

## Project Information

**Project Title:** Computer Vision Based Parking Authorization and Authentication System

**Project Type:** Bachelor of Engineering – Final Year Project


├── parking_system.py       # Python backend, DB initialization, and OCR pipeline
├── parking_system.ino      # Arduino firmware for RFID, Servos, LCD, and Keypad
└── parking_system.db       # Local SQLite database (auto-generated on first run)

# Project Report – HandGestCursor: Hand Gesture Mouse using Computer Vision  
**Course:** CSE3010 – Computer Vision (BYOP)  
**Slot:** C11 + C12  
**Student Name:** Ishaan Mittal  
**Registration No.:** 23BAI10462  
**Faculty:** Dr. Raghavendra Mishra  
**Institution:** VIT Bhopal University  

---

## 1. Problem Statement

Traditional mouse and keyboard input are not always accessible or ergonomic for every user. People with motor disabilities, repetitive‑strain injuries, or simply those seeking a hands‑free interface can benefit from alternative input methods.  
This project implements a **Hand Gesture Mouse** system that uses **computer vision** to detect hand gestures from a standard webcam and map them to mouse actions (movement, click, scroll, drag) and system control (volume, brightness). The goal is to build a low‑cost, software‑only virtual mouse that works in real‑time.

---

## 2. Objectives

- To design a **real‑time hand‑gesture‑based mouse controller** using OpenCV and MediaPipe.
- To detect and classify hand gestures such as:
  - V‑finger for cursor movement.
  - Fist for drag.
  - Individual finger taps for click, right‑click, and double‑click.
  - Pinch gestures for scrolling and system control.
- To control system **volume** and **screen brightness** using hand gestures.
- To provide a **user‑friendly GUI** with webcam preview, controls, and status visualization.
- To align the project with **CSE3010 – Computer Vision** syllabus topics (feature extraction, image processing, and real‑time vision systems).

---

## 3. Methodology

### 3.1. System Architecture

The system follows this pipeline:

1. **Input:** Webcam video stream (`OpenCV` `cv2.VideoCapture`).
2. **Landmark Extraction:** `MediaPipe Hands` detects hand landmarks in real‑time (21 points per hand).
3. **Feature Computation:** Relative distances and angles between landmarks are computed to decide finger states (open/closed).
4. **Gesture Classification:** Based on feature values, gestures (`V_GEST`, `FIST`, `PINCH`, etc.) are classified.
5. **Action Mapping:** Each gesture is mapped to:
   - Mouse movement.
   - Mouse clicks and drag.
   - Scroll actions.
   - Volume and brightness changes.
6. **Output:** A PyQt5 GUI shows the live camera feed with drawn hand skeletons and status text.

### 3.2. Hand Gesture Logic

- **HandRecog class** encodes finger states from landmark positions.
- Finger states are converted into **binary gesture codes** (`Gest` enum).
- **Controller class** maps these codes to actions:
  - `V_GEST` → cursor move.
  - `FIST` → drag (mouse‑down + move).
  - `MID` → left‑click.
  - `INDEX` → right‑click.
  - `TWO_FINGER_CLOSED` → double‑click.
  - `PINCH_MINOR` → scroll.
  - `PINCH_MAJOR` → volume and brightness changes.

Distances between key landmarks (e.g., thumb‑tip to index‑tip, index‑tip to middle‑tip) are used to detect pinch and spread gestures with a threshold model.

### 3.3. System Control (Volume & Brightness)

- `pycaw` is used to get and set system master volume (Windows only).
- `screen_brightness_control` is used to get and set display brightness.
- Pinch‑major gesture (`Gest.PINCH_MAJOR`) increases/decreases these values gradually based on pinch‑distance changes.

---

## 4. Implementation

### 4.1. Software Stack

- **Python 3.12** (backend logic).
- **OpenCV** – video capture and image processing.
- **MediaPipe Hands** – hand landmark detection.
- **pyautogui** – mouse control.
- **pycaw** – Windows audio control.
- **screen_brightness_control** – brightness control.
- **PyQt5** – GUI for live video and controls.

### 4.2. Files

- `main.py` – PyQt5 GUI launcher that uses the above module to run the hand‑gesture mouse.
- `requirements.txt` – Lists all dependencies for easy install.
- `README.md` – VITyarthi‑style instructions and description.
- `docs/project_report.md` – This project report.

---

## 5. Results & Observations

### 5.1. Functional Results

- The system successfully detects **hand gestures** in real‑time and maps them to:
  - Cursor movement with V‑finger pointing.
  - Left‑click, right‑click, and double‑click.
  - Drag‑and‑drop via fist gesture.
  - Scrolling via pinch‑minor.
  - Volume and brightness control via pinch‑major.

- The GUI:
  - Shows live webcam feed with hand landmarks drawn.
  - Provides a status label and toggle controls.
  - Allows sensitivity adjustment and enable/disable mouse control.

### 5.2. Limitations

- Performance depends heavily on **good lighting** and **camera distance** (40–60 cm ideal).
- Background clutter or multiple hands can confuse gesture classification.
- Multi‑monitor setups are not explicitly handled (cursor maps to primary screen).
- `screen_brightness_control` and `pycaw` work best on Windows; Linux/macOS support may vary.

---

## 6. Future Improvements

- **Calibration Routine:** Let the user define hand‑screen mapping by following on‑screen points.
- **Left‑hand / Right‑hand Toggle:** Let the user choose which hand is the “dominant hand” for V‑gesture and pinch‑major.
- **Smoothing:** Add exponential smoothing to cursor movement for lesser jitter.
- **Additional Gestures:** Add gestures for:
  - Opening/closing apps.
  - Window minimization/maximization.
- **Fallback Mode:** Provide a keyboard‑shortcut toggle to disable the gesture mouse quickly.

---

## 8. Conclusion

This project successfully implements a **computer‑vision‑based hand‑gesture mouse** that maps real‑time hand landmarks from MediaPipe to mouse actions and system control. The project aligns with the **CSE3010 – Computer Vision** syllabus by demonstrating:

- Real‑time image and video processing.
- Feature extraction from landmarks.
- Gesture classification and mapping.
- Practical integration of CV‑based input into an interactive GUI application.

It provides a foundation for future work in **accessibility‑oriented HCI** and **gesture‑based human–computer interfaces**.

---

## References

- OpenCV: https://opencv.org  
- MediaPipe: https://developers.google.com/mediapipe  
- PyAutoGUI: https://pyautogui.readthedocs.io  
- pycaw: https://github.com/AndreMiras/pycaw  
- screen_brightness_control: https://pypi.org/project/screen-brightness-control  
- VITyarthi BYOP Guidelines – CSE3010, Computer Vision.

# HandGestCursor – Hand Gesture Mouse using Computer Vision

**Course:** Computer Vision (CSE3010) – BYOP (Bring Your Own Project)  
**Enrolled in:** VITyarthi Platform – Slot C11 + C12  
**Student:** Ishaan Mittal | 23BAI10462  
**Faculty:** Dr. Raghavendra Mishra  
**Institution:** VIT Bhopal University

---

## 📌 Project Overview

**HandGestCursor** is a hand‑gesture‑based virtual mouse that uses **computer‑vision techniques** (OpenCV + MediaPipe) to detect hand landmarks and translate them into mouse actions such as:

- Cursor movement using index‑finger‑like pointing (V‑gesture).
- Left‑click, right‑click, and double‑click using different finger gestures.
- Dragging the cursor by making a **fist**.
- Scrolling using **pinch‑minor** (vertical pinch).
- Controlling system **volume** and **screen brightness** using **pinch‑major** gesture.

The system runs on a standard webcam and requires no extra hardware, making it an accessible, hands‑free interface.

---

## 📦 Technologies & Libraries Used

- **Python 3.x** (tested on 3.10+)
- **OpenCV** – webcam capture, real‑time video processing
- **MediaPipe Hands** – hand landmark detection
- **pyautogui** – mouse movement, clicking, dragging, and scrolling
- **pycaw** – system volume control
- **screen‑brightness‑control** – monitor brightness control
- **PyQt5** – GUI with live video panel, controls, and status

---

## 🧩 Gesture Mapping (Supported Actions)

| Gesture            | Hand Location | Action |
|--------------------|---------------|--------|
| V‑finger (index‑middle extended) | Major hand | Move cursor |
| Single fist        | Major hand | Drag (press‑and‑hold left‑click) |
| Mid‑finger tap     | Major hand | Left‑click |
| Index‑finger tap   | Major hand | Right‑click |
| Two‑finger‑closed  | Major hand | Double‑click |
| Horizontal pinch   | Minor hand | Horizontal scroll |
| Vertical pinch     | Minor hand | Vertical scroll |
| Pinch‑major (both hands) | Major hand | Change volume & brightness |

Pinch thresholds are tuned for smooth volume and brightness adjustments.

---

## ⚙️ Setup & Installation

1. Ensure you have **Python 3.9+** installed on your machine.

2. Clone the repository:

   ```bash
   git clone https://github.com/kalicharm21/HandGestCursor-Hand-Gesture-Based-Mouse-and-System-Control
   cd hand-gesture-mouse-byop
   ```

3. (Optional) Create a virtual environment:

   ```bash
   python -m venv venv
   venv\Scripts\activate      # Windows
   source venv/bin/activate   # Linux/macOS
   ```

4. Install required packages:

   ```bash
   pip install -r requirements.txt
   ```

   If needed, `requirements.txt` contains:

   ```txt
   opencv-python
   mediapipe
   pyautogui
   PyQt5
   numpy
   pycaw
   screen-brightness-control
   ```

---

## ▶️ How to Run

1. Open a terminal inside the project folder:

   ```bash
   python main.py
   ```

2. Place your hand in front of the webcam:
   - Ensure good lighting and avoid direct backlight.
   - Keep your hand about 40–60 cm from the camera.

3. In the GUI:
   - The left panel shows:
     - **Status** (Active / Paused).
     - **Sensitivity** slider (tunes cursor speed).
     - **Enable mouse control** checkbox.
     - **Right hand major** toggle (choose which hand defines V‑gesture and pinch‑major).
   - The right panel shows the live webcam feed with drawn hand landmarks.

4. Use hand gestures as described above to control:
   - Mouse cursor.
   - Clicks and scroll.
   - System volume & brightness (if supported by your OS).

---

## 🧑‍🏫 Target Users

- **Accessibility‑focused users** who benefit from touch‑free interaction.
- **HCI researchers** exploring gesture‑based input.
- **Students of CSE, AI, and HCI** studying computer‑vision‑based interaction.

---

## 🧠 Syllabus Coverage (CSE3010 – Computer Vision)

This project demonstrates and applies key computer‑vision concepts:

- Real‑time **video capture and processing** (OpenCV).
- **Hand landmark detection** using MediaPipe.
- **Feature extraction** from finger positions and distances.
- **Gesture classification** logic (V‑gesture, pinch, fist, etc.).
- **Real‑time mapping** of visual features to system actions.
- **MediaPipe + OpenCV integration** for production‑style applications.

---

## 📁 Repository Structure

```text
hand-gesture-mouse-byop/
│
├── main.py                    ← PyQt5 GUI launcher for gesture mouse
├── requirements.txt           ← Python dependencies
├── docs/
│   └── project_report.md      ← Project report (can be exported as PDF)
└── README.md                  ← This file (VITyarthi‑style README)
```

---

## 📄 Project Report Submission

A separate project report document is maintained in `docs/project_report.md` (or `.pdf`) with:
- Problem statement
- Objectives
- Methodology
- Results & observations
- Future improvements
- Screenshots

This report is submitted via the **VITyarthi platform** along with this GitHub repo link.

---

## 📸 Screenshots (Recommended)

In your report, include:
- Screenshot of the running GUI with hand gesture overlay.
- Screenshot of `requirements.txt` installation.
- Short demo screenshots of:
  - Cursor movement with V‑gesture.
  - Clicking, double‑clicking.
  - Volume / brightness change with pinch‑major.

---

## 📝 Important Notes

- This project **requires a webcam** and **Python 3.9+**.
- Ensure:
  - `pyautogui` is allowed to control the mouse (no conflicting mouse‑blocking software).
  - Your laptop brightness driver is supported by `screen_brightness_control`.
- This code is **for educational use** and fits CSE3010 BYOP standards.

---

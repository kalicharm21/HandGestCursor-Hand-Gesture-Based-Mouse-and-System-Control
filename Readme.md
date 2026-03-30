# HandGestCursor – Hand Gesture Mouse using Computer Vision

**HandGestCursor** is a **hand‑gesture‑based virtual mouse** that uses **computer‑vision techniques** (OpenCV + MediaPipe) to detect hand landmarks and translate them into mouse actions such as:

- Cursor movement using index‑finger‑like pointing (`V‑gesture`).
- Left‑click, right‑click, and double‑click using different finger gestures.
- Dragging the cursor by making a **fist**.
- Scrolling using **pinch‑minor** (vertical or horizontal pinch).
- Controlling system **volume** and **screen brightness** using **pinch‑major** gesture.

The system runs on a standard webcam and requires no extra hardware, making it an accessible, hands‑free interface that feels like a true “mouse‑controlled‑by‑hand” experience.

---

## Features (Detailed)

### 1. Hand‑based mouse control
- **V‑finger (index‑middle extended)**  
  - When the index and middle fingers are extended and others are folded, the system detects a **V‑gesture**.  
  - The tip of the middle finger is used as the **pointer location**.  
  - The GUI translates this position into screen coordinates and moves the cursor smoothly.

- **Fist (all fingers folded)**  
  - When all fingers are folded into a fist, the system enters **drag mode**.  
  - This is treated as a **mouse‑down + hold** state; the cursor follows your hand while the fist is held.
  - When the fist is released (hand opens to `PALM`), the system sends **mouse‑up**, stopping the drag.

- **Mid‑finger tap**  
  - The index finger is folded while others are open.  
  - On detection, the system sends a **left‑click** at the current cursor position.

- **Index‑finger tap**  
  - Only the index finger is extended; the rest are folded.  
  - On detection, the system sends a **right‑click** at the current position.

- **Two‑finger‑closed**  
  - Index and middle fingers are folded; the rest are open.  
  - On detection, the system sends a **double‑click** (simulating a quick double‑tap).

### 2. Scrolling and system control
- **Pinch‑minor**  
  - When the thumb and index finger are pinched close together, the system detects a **pinch‑minor** gesture.  
  - Vertical pinch movement (up‑down) controls **vertical scroll** (mouse wheel).  
  - Horizontal pinch movement (left‑right) controls **horizontal scroll** (shift + scroll on many systems).

- **Pinch‑major**  
  - When the thumb and index are strongly pinched and moved vertically, the system detects **pinch‑major**.  
  - Horizontal pinch‑motion controls **system volume** (Windows, via `pycaw`).  
  - Vertical pinch‑motion controls **screen brightness** (Windows, via `screen_brightness_control`).

### 3. GUI with live feedback
The PyQt5 GUI provides:
- **Live webcam panel** with hand landmarks and gesture labels overlaid.
- **Status label** that shows `Active` / `Paused`.
- **Sensitivity slider** that tunes how fast the cursor moves per hand motion.
- **Enable mouse control checkbox** to safely turn off all mouse actions when testing.
- **Right hand major checkbox** to choose which hand controls the V‑gesture and pinch‑major (default: right hand).

This lets you run, debug, and tune the system without touching the code.

---

## Technologies & Libraries Used (Detailed)

- **Python 3.x (3.10+ recommended):**  
  - Primary language for all logic and GUI.

- **OpenCV (`cv2`):**  
  - Captures video from the webcam using `cv2.VideoCapture(0)`.  
  - Converts frames between BGR and RGB for MediaPipe processing.  
  - Draws debug shapes (circles, text, landmarks) on the live feed.

- **MediaPipe Hands (`mediapipe`):**  
  - Detects hand landmarks (21 points per hand) in real‑time.
  - Provides `landmark.x`, `landmark.y`, `landmark.z` coordinates normalized to image size.

- **pyautogui:**  
  - `pyautogui.moveTo(x, y, duration=0.07)` smoothly moves the mouse pointer.  
  - `pyautogui.mouseDown()` / `pyautogui.mouseUp()` enable **drag** behavior.  
  - `pyautogui.click()`, `pyautogui.click(button='right')`, and `pyautogui.doubleClick()` handle mouse clicks.  
  - `pyautogui.scroll()` handles wheel‑scroll for pinch‑minor.

- **pycaw (`pycaw`):**  
  - Controls system master volume by:
    - Getting the master volume level.
    - Adjusting it by a small delta based on pinch‑movement.
    - Setting it back via `SetMasterVolumeLevelScalar`.

- **screen_brightness_control:**  
  - Sets screen brightness by:
    - Reading current brightness (0–100%).
    - Adjusting it by a small delta based on pinch‑movement.
    - Applying the new brightness level immediately.

- **PyQt5:**  
  - Builds a responsive GUI with:
    - A main `QWidget` window.
    - `QLabel` for live video.
    - `QPushButton`, `QSlider`, `QCheckBox` for controls.
    - `QTimer` for continuous frame updates.
  - Uses `pyqtSignal` to pass frames from OpenCV to the GUI safely.

- **numpy (`numpy`):**  
  - Handles array math for:
    - Distance calculations between landmark points.
    - Normalizing pixel coordinates for gesture detection.

---

## Gesture Mapping and Logic (Technical Details)

### 1. Landmark‑based gesture detection
Each gesture is detected by measuring relative distances between key landmarks:

- **Finger state (`set_finger_state`)**  
  - For each finger (index, middle, ring, pinky), the system compares:
    - Distance from finger‑tip to mid‑joint.
    - Distance from mid‑joint to base.
  - A **ratio** of these distances decides if the finger is:
    - Open (`ratio > 0.5`) → bit set.
    - Folded (`ratio ≤ 0.5`) → bit cleared.
  - These bits combine into a compact integer (`self.finger`), which is matched against `Gest` enum values.

- **V‑gesture detection**  
  - When only index and middle are open (`self.finger == Gest.FIRST2`) and they are extended, the system identifies **V‑gesture** (`Gest.V_GEST`).  
  - The position of the middle‑finger tip (or index‑tip) is mapped to screen space.

- **Pinch detection**  
  - When thumb‑tip and index‑tip are close (`distance < 0.05`), the system sees a **pinch**.  
  - Whether it’s `PINCH_MINOR` or `PINCH_MAJOR` depends on which hand is “major”.  
  - Vertical/horizontal pinch difference is decided by:
    - `getpinchxlv()` and `getpinchylv()` compute relative pinch‑movement.  
    - `pinchthreshold` filters out small jitter.

- **Gesture stability**  
  - A simple frame‑count system (`self.frame_count > 4`) ensures the same gesture persists for at least 4 frames before being accepted, avoiding flicker.

### 2. Mouse coordinate mapping
Given a hand landmark at `(x, y)`:

```python
point = 9  # middle‑finger base landmark
sx, sy = pyautogui.size()  # screen width, height
px = int(x * sx)
py = int(y * sy)
```

The gesture controller:
- Compares `px` and `py` with the last known position.  
- Applies **non‑linear scaling** (small moves slow, big moves fast) for smooth dragging.  
- Feeds `(px, py)` to `pyautogui.moveTo()` or click events.

---

## Setup & Installation (Detailed)

### 1. Prerequisites

- **Operating System**
  - Tested on **Windows 10/11** (full mouse + volume + brightness).
  - Works on **Linux/macOS** for mouse only (volume / brightness may require extra setup).

- **Hardware**
  - Standard webcam (built‑in laptop cam or USB).

- **Permissions**
  - On **Windows**, most likely no extra permissions are needed.
  - On **macOS**, you may need to grant **Accessibility** permission to `pyautogui` (via System Settings → Privacy & Security → Accessibility).

- **Python**
  - Python 3.9+ (3.10–3.12 strongly recommended).  
  - You can check with:
    ```bash
    python --version
    ```

### 2. Clone the Repository

```bash
git clone https://github.com/kalicharm21/HandGestCursor-Hand-Gesture-Based-Mouse-and-System-Control.git
cd HandGestCursor-Hand-Gesture-Based-Mouse-and-System-Control
```

### 3. (Optional) Create a Virtual Environment

A virtual environment prevents conflicts with other projects.

```bash
# Create virtual environment
python -m venv venv

# Activate on Windows (PowerShell / cmd)
venv\Scripts\activate

# Activate on Linux/macOS
source venv/bin/activate
```

From now on, all `pip` and `python` commands will use this environment.

### 4. Install Dependencies

```bash
pip install -r requirements.txt
```

If `requirements.txt` does not exist yet, create it with:

```txt
opencv-python
mediapipe
pyautogui
PyQt5
numpy
pycaw
screen-brightness-control
```

You can verify everything is installed:

```bash
python -c "import cv2, mediapipe, pyautogui, PyQt5, numpy, pycaw, screen_brightness_control as sbc; print('All imports OK')"
```

### 5. (Optional) Enable Accessibility (macOS only)

1. Open `System Settings → Privacy & Security`.  
2. Go to **Accessibility**.  
3. Click the `+` and add `Terminal` (or your Python IDE / shell).  
4. Restart the terminal.

This lets `pyautogui` control the mouse.

---

## How to Run the Project (Step‑by‑Step)

### 1. Start the program

From the project folder (inside an active terminal):

```bash
python main.py
```

The GUI window will appear. It may take 2–3 seconds to load MediaPipe models.

### 2. Understand the GUI layout

- **Left panel – Controls**
  - **Status label:**  
    - Shows `Active` when the webcam is running.
    - Shows `Paused` when you click the pause button.
  - **Sensitivity slider:**  
    - From `10–100` (tuned via `self.sens_slider.getValue()`).  
    - Higher values → cursor moves faster for the same hand motion.
  - **Enable mouse control checkbox:**  
    - When **checked**, mouse actions are enabled (move, click, scroll).  
    - When **unchecked**, you can see gestures but no mouse moves (good for debugging).
  - **Right hand major checkbox:**  
    - When checked, the **right hand** is treated as the major hand for `V‑gesture` and `pinch‑major`.
    - When unchecked, the **left hand** is major (hand‑chirality toggle).

- **Right panel – Live feed**
  - Shows the webcam feed flipped horizontally (like a mirror).
  - Hand landmarks are drawn as green circles and lines.
  - Gesture names (e.g., `V_GEST`, `FIST`) may be overlaid for debugging.

### 3. Calibrate your environment

- **Lighting:**  
  - Avoid strong backlight.  
  - Prefer even, front lighting.
- **Distance:**  
  - Keep the camera about 40–60 cm from you.  
  - Too far → small, noisy landmark positions.  
  - Too close → hand may be cropped.
- **Hand visibility:**  
  - Wear solid‑colored clothing (no camo patterns).  
  - Avoid reflective surfaces behind you.

### 4. Use each gesture

#### a) V‑finger (index + middle extended)

- Make a **peace‑sign** with index and middle fingers extended.  
- Keep others folded.  
- Slowly move your hand in front of the camera.

You should see:
- Cursor smoothly following your hand.  
- No clicks or scroll.

#### b) Fist (drag)

- Make a **closed fist** with the dominant hand.  
- The system will:
  - Press and hold the left mouse button.  
  - Follow your hand as you move it.  
- Release the fist → drag stops.

This works like pressing the mouse left‑button and dragging an icon or window.

#### c) Mid‑finger tap (left‑click)

- Fold the index finger down, keep others open.  
- Briefly “tap” with the middle‑finger forward.

System behavior:
- Sends **single left‑click** at the current cursor position.

#### d) Index‑finger tap (right‑click)

- Only the index finger is extended; others are folded.  
- Tap forward once.

System behavior:
- Sends **right‑click** (context menu).

#### e) Two‑finger‑closed (double‑click)

- Fold index and middle; others open.  
- Tap once.

System behavior:
- Simulates **double‑click** (often opens an icon or file quickly).

#### f) Pinch‑minor (scroll)

- Pinch the thumb and index together.  
- Move them up‑down for **vertical scroll** (like mouse wheel).  
- Move them left‑right for **horizontal scroll** (shift + scroll in many apps).

#### g) Pinch‑major (volume + brightness)

- On **Windows**:
  - Pinch the thumb and index strongly.  
  - Move vertically to change **brightness**.  
  - Move horizontally to change **volume**.

- If nothing happens:
  - Ensure `pycaw` and `screen_brightness_control` imported without error.  
  - Try running as admin (Windows) or with `sudo` (Linux) once.

### 5. How to stop the program

- **Recommended:** Click the close button on the window.  
- Or press `Ctrl+C` in the terminal.

The webcam will be released, and all resources cleaned up.

---

## Usage Tips & Troubleshooting

### 1. Cursor not moving

- Verify `pyautogui` is allowed to control the mouse:
  - On Windows, try running the terminal with **administrator** privileges once.  
  - On macOS, enable **Accessibility** for the terminal/IDE.  
- Ensure `pyautogui.FAILSAFE = False` in your code (already set).  
- Try a **simpler test script** (just `pyautogui.moveTo(100, 100)` from Python to confirm it works).

### 2. Gestures not detected

- Improve lighting:
  - Avoid side‑backlighting.  
  - Use a lamp or daylight.
- Adjust distance:
  - 40–60 cm from the camera.  
  - No other hands or cluttered background.
- Confirm MediaPipe detects at least one hand:
  - Check that `results.multi_hand_landmarks` is not `None`.

### 3. Volume / brightness not changing

- Best on **Windows**.  
- On Linux/macOS:
  - `pycaw` and `screen_brightness_control` may not work directly.  
  - You can comment out or disable those parts of `Controller` if you only want mouse control.

### 4. Hand flips or jitter

- Increase `self.sens_slider` value for slower movement.  
- Add a small moving‑average filter on `px` and `py` (simple `alpha`‑blend with previous position).  
- Ensure steady hand motion and good lighting.

---

## Repository Structure

Here’s what each file does:

```text
HandGestCursor-Hand-Gesture-Based-Mouse-and-System-Control/
│
├── main.py                     ← Main entry point.
│   - Contains:
│     - `HandRecog` class (gesture detection).
│     - `Controller` class (pyautogui / pycaw / brightness logic).
│     - `HandMouseWidget` (PyQt5 GUI).
│   - Runs when you do `python main.py`.

├── requirements.txt            ← Python dependencies.
│   - Lists all packages needed for installation.

├── docs/
│   └── project_report.md       ← Optional project report (for your own notes or PDF export).

└── README.md                   ← This file (setup, usage, and explanation).
```

---


# cpp_task.cpp

The code run two independent tasks in separated threads, with a loop and timeout. At the end, counter values `loop_counter1` and `loop_counter2` will be printed.

Threads use same shared variable `my_running`.

The `my_thread1` will loop `loop_counter1` (with sleep 2s per iteration) until `timeout` (>=10s) or if `running` is changed outside.

The `my_thread2` will loop `loop_counter2` (with sleep 1s per iteration) up to 5 (if case) or until `timeout` (>=10s).

For this ``empty`` code the threads will stop on `loop_counter2` = 5 and `loop_counter1` = 3 by changin `my_running` value due to `loop_counter2` = 5.

---

**Issues:
** 1. Lambda at line 17 captures everything by reference `[&]`
should be: `[&running, Process, timeout]` (simple way exactly for this code) or `[running, Process, timeout]` (with <memory> and `shared_ptr` for `running`, which is hinted in code).


# python_task.py

Main problem of proposed code is overwriting of some values in matrix before reading them.

There are different methods how to solve it:

---

**1. With idea that we can modify code only in function without additional matrix, which I apply in python_task.py:**

```python
def rotate_in_place(matrix):
    n = len(matrix)
    for r in range(n):
        for c in range(r+1, n):
            matrix[c][r], matrix[r][c] = matrix[r][c],  matrix[c][r]

    for r in range(n):
        matrix[r].reverse()
```

**1.a With additional matrix:**

```python
def rotate_in_place(matrix):
    n = len(matrix)
    new_matrix = [row[:] for row in matrix]
    for r in range(n):
        for c in range(n):
            matrix[r][c] = new_matrix[n-c-1][r]
```

---

Other thoughts:

---

**2. I widely use NumPy for scientific problems. So with NumPy it possible as:**

```python
np.rot90(np.array(matrix), k=-1).tolist()
```

---

**3. If it is about pictures and opencv library, there are few more options:**

- 3.a. `cv2.rotate()`
- 3.b. `cv2.transpose()` and `cv2.flip()`
- 3.c. `cv2.warpAffine()` - useful for any rotations with scaling and so on.

# Catch an UAV

## Available Hardware

- Robot Arm
- Gripper system capable for holding an UAV
- Gripper-integrated full HD camera

---

## 1. Tracking & Capture Algorithm Pipeline

**Stage 1 — Detection (2D)**
- A deep learning detector (YOLO forexample) localises the drone in the image frame.
- Input: full HD frame; output: 2D bounding box + confidence.

**Stage 2 — 3D Pose Estimation (2D → 3D)**
- PnP (with markers on drone or knowing geometry).

    `cv2.solvePnP` allows to receive `[X, Y, Z]` coordinates relative to the camera.

- monocular depth DNN (MiDaS/DepthAnything) with relative depth per pixel

    useful more for unknown drones

**Stage 3 — State Estimation & Prediction**

- Kalman Filter should be enough or Extended Kalman Filter for prediction of time and place for catching UAV. Prediction allow compensation of arm latency.

**Stage 4 — Arm Control**

- Arm aims at future drone positions, not current, so KF state is important.
- It could be 3 stages:
    1) Far Range: KF predict intercept point. MPC plans the arm movement. IK converts planned position to joint angles.
    2) Close range: Camera take direct control for detection of actual drone position via PnP.
    3) Contact: F/T sensor detects touch. The arm stops, gripper closes. Drone is lifted.

## 2. Challenges of a Single Camera

### Depth ambiguity (main)

Camera sees 2D, Z is lost. PnP help to solve it with known drone geometry or markers.

### Processing latency

Drone still moves after frame captured and processed.


### Motion blur

Fast drone smears the image and cant be detected.

### Small size on distance, Light variation, etc.


## 3. Alternative sensors

### short-range LiDAR (my choise)

Low latency, lighting independent. Ideal for last stages of catching.

### UWB-tag

Perfect if a drone cooperates. Good accurate for 3D position, low latency. Good for first and second stages.






p.s. Hope to discuss more detailed on an interview stage.
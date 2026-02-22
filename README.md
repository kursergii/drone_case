# cpp_code.cpp

The code run two independent tasks in separated threads, with a loop and timeout. At the end, counter values `loop_counter1` and `loop_counter2` will be printed.

Threads use same shared variable `my_running`.

The `my_thread1` will loop `loop_counter1` (with sleep 2s per iteration) until `timeout` (>=10s) or if `running` is changed outside.

The `my_thread2` will loop `loop_counter2` (with sleep 1s per iteration) up to 5 (if case) or until `timeout` (>=10s).

For this ``empty`` code the threads will stop on `loop_counter2` = 5 and `loop_counter1` = 3 by changin `my_running` value due to `loop_counter2` = 5.

---

**Issues:
** 1. Lambd at line 17 captures everything by reference `[&]`
should be: `[&running, Process, timeout]` (simple way exectly for this code) or `[running, Process, timeout]` (with <memory> and `shared_ptr` for `running`, which is hinted in code).

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

- 4.a. `cv2.rotate()`
- 4.b. `cv2.transpose()` and `cv2.flip()`
- 4.c. `cv2.warpAffine()` - useful for any rotations with scaling and so on.
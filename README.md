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


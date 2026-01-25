# EXPERIMENT 103

## Experiment Title  
**Thread Coordination Using Condition Variables in QNX**

---

## Objective  
To implement a multi-threaded state machine using condition variables and study coordinated execution among threads in a QNX environment.

---

## Problem Statement  

In this project, the file `condvar.c` is provided.

Students must modify this program to implement a **four-state state machine** with the following requirements:
<img width="228" height="180" alt="Screenshot 2026-01-26 at 00 20 22" src="https://github.com/user-attachments/assets/565dfbcb-c4eb-4643-8084-753a873192ed" />

- Four threads must be created, each responsible for handling one state.
- Only **one condition variable** must be used for synchronization.
- State 1 must maintain a counter.
- Based on the counter value, State 1 should transition control to State 2 or State 3.
- The remaining states must complete the cycle and return control appropriately.

This exercise emphasizes controlled thread execution using condition variables rather than busy waiting.

---

## Tasks to be Performed  

1. Navigate to the `proc_thread` project directory.  
2. Open the file `condvar.c`.  
3. Study the existing thread structure and synchronization logic.  
4. Design a four-state state machine model.  
5. Modify the program to create four threads, each assigned to one state.  
6. Introduce a single condition variable for synchronization.  
7. Implement a shared state variable and counter.  
8. Ensure that only the thread corresponding to the active state proceeds.  
9. Implement transitions from State 1 to State 2 or State 3 based on the counter value.  
10. Rebuild and run the modified program.  
11. Observe and record the order of execution and state transitions.  
12. Verify that busy waiting is avoided and threads block correctly on the condition variable.

---

## Expected Outcome  

The program should exhibit orderly transitions among four states, controlled by a single condition variable and a shared counter.  
Each thread must execute only when its corresponding state becomes active, demonstrating proper condition-variable-based synchronization.

---




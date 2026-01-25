# EXPERIMENT 102


## Experiment Title  
**Synchronization Using Mutexes in QNX**

---

## Objective  
To study race conditions in multithreaded programs and resolve them using mutex locks to control access to critical sections.

---

## Problem Statement  

In the `proc_thread` project, two programs are provided:

- `nomutex.c`
- `mutex_sync.c`

The program `nomutex.c` creates multiple threads that access shared resources without synchronization, which may result in inconsistent output.

Students are required to observe this behavior and then modify `mutex_sync.c` to correct the problem by introducing proper mutex-based synchronization.

---

## Tasks to be Performed  

1. Navigate to the `proc_thread` project directory.  
2. Open and inspect the file `nomutex.c`.  
3. Run the program with the default number of threads.  
4. Modify the constant `NUMTHREADS` to `4`.  
5. Rebuild and run the program again.  
6. Observe and record the changes in behavior, especially unequal or interleaved `printf()` outputs.  
7. Open the file `mutex_sync.c`.  
8. Add a mutex to protect the critical sections.  
9. Insert lock and unlock operations at appropriate locations in the code.  
10. Ensure that the mutex is not held longer than necessary.  
11. Rebuild and execute the corrected program.  
12. Compare the output with the unsynchronized version.  
13. Analyze how mutex usage affects performance.

---

## Expected Outcome  

The unsynchronized program should display inconsistent or interleaved output due to race conditions.  
After introducing a mutex, the output should become orderly and consistent, demonstrating correct synchronization.  
Students should observe the impact of locking on execution time and responsiveness.

---

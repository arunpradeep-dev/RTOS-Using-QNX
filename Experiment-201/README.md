# EXPERIMENT-201

## Experiment Title  
**Process Creation Using fork() and Parent–Child Execution in QNX**

---

## Aim/Objective  
To create multiple child processes using the `fork()` system call, observe different execution paths in parent and child processes, terminate the parent after a fixed time interval, and print the child process ID after the parent exits.

---

## Problem Statement  

Write a C program that creates multiple child processes using the `fork()` system call.

The program must satisfy the following requirements:

- The parent process should create more than one child process.
- The parent and child processes must print different identifying messages.
- The parent process must terminate after **5 seconds**.
- Each child process must continue executing and print its **process ID (PID)** after the parent exits.

This experiment demonstrates UNIX/QNX process creation semantics and independent execution of parent and child processes.

---

## Tasks to be Performed/Algorithm  

1. Write a C program that uses the `fork()` system call to create multiple child processes.  
2. Include different print statements in the parent and child code paths.  
3. Introduce a delay mechanism so that the parent terminates after 5 seconds.  
4. Ensure that the child processes remain alive after the parent exits.  
5. Print the PID of each child process after the parent terminates.  
6. Compile the program using the QNX compiler toolchain.  
7. Execute the program and observe the output.  
8. Record the order of messages printed by the parent and child processes.  
9. Verify that the child processes continue running independently of the parent.

---

## Expected Outcome/Sample Output  

- The parent process should create multiple children and print its execution messages.  
- After five seconds, the parent process should terminate.  
- The child processes should continue executing and display their respective PIDs.  
- The output must clearly distinguish between parent and child execution flows.

---

## Program

---

## Output

---

## Result

---

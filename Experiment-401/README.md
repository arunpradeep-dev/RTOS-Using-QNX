# EXPERIMENT-401

## Experiment Title  
**Simple Interrupt Handling Using InterruptWait() in QNX (Momentics IDE)**

---

## Objective  
To implement basic interrupt handling in QNX by attaching an interrupt service thread (IST) and using `InterruptWait()` to receive interrupt notifications.

---

## Problem Statement  

In the `interrupt` project, a skeleton source file named `intsimple.c` is provided.

Students must complete the program to handle hardware interrupts. The instructor will specify the interrupt number (IRQ) to attach to.

The completed program must:

- Attach an interrupt service thread (IST) to the given interrupt.
- Wait for interrupt notifications using `InterruptWait()` inside a loop.
- Display a message each time an interrupt is received.

---

## Tasks to be Performed  

1. Open the `interrupt` project in QNX Momentics IDE.  
2. Locate and open the skeleton file `intsimple.c`.  
3. Search for comments / `TODO` markers indicating incomplete sections.  
4. Add code to:
   - attach an interrupt to the specified IRQ (as given by the instructor)
   - create / attach an Interrupt Service Thread (IST)
5. In the main execution loop, use `InterruptWait()` to block until an interrupt notification occurs.  
6. Print a message whenever an interrupt is received.  
7. Build the project in Momentics.  
8. Run the program and observe interrupt notifications.  
9. Record observations (frequency / count of interrupts and console output).

---

## Run / Test Steps (Momentics IDE)

1. Right-click the application binary → **Run As → QNX C/C++ Application**.  
2. Trigger the specified interrupt source (as instructed).  
3. Verify that each interrupt causes:
   - `InterruptWait()` to return
   - a console message to be printed

---

## Expected Outcome  

- The program successfully attaches to the specified interrupt source.  
- The interrupt service thread receives notifications.  
- `InterruptWait()` unblocks on each interrupt and the program prints confirmation.  

---

## Program

---

## Output

---

## Result

---


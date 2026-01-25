# EXPERIMENT-402

## Experiment Title  
**Repeating Timer Using Pulses in QNX (Momentics IDE)**

---

## Objective  
To implement a repeating timer in QNX that delivers periodic notifications using pulses, starting after an initial delay and continuing at a fixed interval.

---

## Problem Statement  

In the `time` project, a source file named `reptimer.c` is provided.

When completed, the program must behave as follows:

- The program should wake up **5 seconds** after it starts running.
- After that, it should wake up **every 1500 milliseconds (1.5 seconds)**.
- The wake-up mechanism must be through **receiving a pulse**.

The code in `main()` already includes:
- setup of the pulse event structure
- receiving and handling the pulse

However, the code to:
- create the timer
- start the timer ticking  
is **missing** and must be added.

---

## Tasks to be Performed  

1. Open the `time` project in QNX Momentics IDE.  
2. Locate and open `reptimer.c`.  
3. Identify the section where timer creation and timer start logic is missing (look for comments / `TODO`).  
4. Add the required code to:
   - create the timer
   - configure it to deliver a pulse
   - set an initial expiry of **5 seconds**
   - set a repeating interval of **1500 ms**
5. Build the project in Momentics.  
6. Run the application and observe the pulse-driven output messages.  
7. Verify the timing behavior using the console timestamps (or manual time observation).

---

## Build Steps (Momentics IDE)

1. **Project → Build Project** (or Hammer icon).  
2. Confirm successful compilation without errors.

---

## Run / Test Steps (Momentics IDE)

1. Run the program (target console / Run As in Momentics):

   ```bash
   ./reptimer

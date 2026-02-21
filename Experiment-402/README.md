# EXPERIMENT-402

## Experiment Title
**Repeating Timer Using Pulses in QNX (Momentics IDE)**

---

## Objective
To implement a periodic timer in QNX that generates pulse notifications using a kernel timer and to handle these pulses using `MsgReceive()`.

---

## Problem Statement
In this project, a skeleton source file named `reptimer.c` is provided.

Learners must complete the missing code to create and start a repeating timer.

The completed program must:

* Create a kernel timer.
* Configure the timer to send a pulse event.
* Generate the first pulse **5 seconds after program start**.
* Continue generating pulses **every 1500 milliseconds (1.5 seconds)**.
* Receive the pulse using `MsgReceive()` and display a message each time the timer expires.

---

## Tasks to be Performed
1. Open the **time** project in QNX Momentics IDE.
2. Locate and open the skeleton file `reptimer.c`.
3. Search for comments or `TODO` markers indicating incomplete sections.
4. Add code to:

   * create a timer using the appropriate QNX timer API
   * configure the timer to deliver the already initialized pulse event
   * set the initial expiry time to **5 seconds**
   * configure the timer interval to **1500 milliseconds**
   * start the timer
5. Ensure the program waits for pulse messages using `MsgReceive()` inside the main loop.
6. Print a message whenever the timer pulse is received.
7. Build the project in Momentics.
8. Run the program and observe periodic pulse notifications.
9. Record observations (time of first pulse and interval between subsequent pulses).

---

## Build / Run / Test Steps

1. Right-click the application binary → **Run As → QNX C/C++ Application**.
2. Execute the program: `reptimer`
3. Verify that:

   * The first message appears **approximately 5 seconds** after execution.
   * Subsequent messages appear **every 1.5 seconds**.
   * Each timer expiry results in a pulse being received and a console message being printed.

---

## Expected Outcome

* The program successfully creates and starts a periodic timer.
* The timer sends pulse notifications to the process.
* `MsgReceive()` unblocks whenever the timer expires.
* Console output confirms periodic execution at the configured intervals.

---

## Program

---

## Output

---

## Result

---

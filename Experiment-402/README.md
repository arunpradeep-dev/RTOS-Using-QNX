# EXPERIMENT-402

## Experiment Title
**Repeating Timer Using Pulses in QNX (Momentics IDE)**

---

## Aim/Objective
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

## Tasks to be Performed / Algorithm
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
#### reptimer.c
```c
/*
 * reptimer.c
 * 
 * This program demonstrates how to use a repeating timer to wake up
 * a thread periodically.  In this case we will wake up by receiving
 * a pulse message whenever the timer expires.
 * 
 * This example uses a simple MsgReceive() loop.  It could also have been
 * written as a resource manager, the only difference being the way in 
 * which the pulse would be received.  The set up for the timer is the same.
 * An example of where you might use a timer with a simple MsgReceive()
 * loop like this is in a driver, where the hardware handling driver
 * thread needs to wake up periodically as well as handle interrupts.
 * 
 * You need to add the code to create the timer and start it ticking.
 *
 *  To test it, run it as follows:
 *    reptimer
 *
 */

#include <stdlib.h>
#include <stdio.h>
#include <errno.h>
#include <sys/neutrino.h>
#include <sys/dispatch.h>
#include <unistd.h>
#include <signal.h>
#include <time.h>
#include <string.h>

#define TIMER_PULSE_EVENT (_PULSE_CODE_MINAVAIL + 7)

/* union of all different types of message(s) we will receive (for this
 * exercise we will only be receiving pulses)
 */
typedef union
{
	struct _pulse pulse;
} message_t;

int main(int argc, char *argv[])
{
	rcvid_t rcvid;
	struct sigevent event;
	int chid, coid;
	message_t msg;

	chid = ChannelCreate(_NTO_CHF_PRIVATE);
	if (chid == -1)
	{
		fprintf(stderr, "ChannelCreate failed: %s\n", strerror(errno));
		exit(EXIT_FAILURE);
	}

	/* set up the pulse event that will be delivered to us by the kernel
	 * whenever the timer expires
	 */
	coid = ConnectAttach(0, 0, chid, _NTO_SIDE_CHANNEL, 0);
	if (coid == -1)
	{
		fprintf(stderr, "ConnectAttach failed: %s\n", strerror(errno));
		exit(EXIT_FAILURE);
	}
	SIGEV_PULSE_INIT( &event, coid, 10, TIMER_PULSE_EVENT, 0 );

	/* TODO: Create a timer which will send the above pulse event
	 * 5 seconds from now and then repeatedly after that every
	 * 1500 milliseconds.  The event to use has already been filled in
	 * above and is in the variable called 'event'.
	 */

	while (1)
	{
		/* wait here for the pulse message */
		rcvid = MsgReceive(chid, &msg, sizeof(msg), NULL );
		if (rcvid == -1)
		{
			fprintf(stderr, "MsgReceive failed: %s\n", strerror(errno));
			continue;
		}
		if (rcvid == 0)
		{
			/* we got a pulse */
			switch (msg.pulse.code)
			{
			case TIMER_PULSE_EVENT:
				/* we got our timer pulse */
				printf("got our pulse, the timer must have expired\n");
				break;
			default:
				printf("unexpected pulse code: %d\n", msg.pulse.code);
				break;
			}
		}
	}
}
```
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

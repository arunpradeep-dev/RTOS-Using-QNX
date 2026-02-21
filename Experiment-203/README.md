# EXPERIMENT 203

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
 
1. Open the file `condvar.c`.  
2. Study the existing thread structure and synchronization logic.  
3. Design a four-state state machine model.  
4. Modify the program to create four threads, each assigned to one state.  
5. Introduce a single condition variable for synchronization.  
6. Implement a shared state variable and counter.  
7. Ensure that only the thread corresponding to the active state proceeds.  
8. Implement transitions from State 1 to State 2 or State 3 based on the counter value.  
9. Rebuild and run the modified program.  
10. Observe and record the order of execution and state transitions.  
11. Verify that busy waiting is avoided and threads block correctly on the condition variable.

---
#### condvar.c
```c
/*
 *  condvar.c
 *
 *  Objectives:
 *
 *      condvar.c is currently a two-state example.  The producer (or
 *      state 0) did something which caused the consumer (or
 *      state 1) to run.  State 1 did something which caused
 *      a return to state 0.  Each thread implemented one of
 *      the states.
 *
 *      This example will have 4 states in its state machine 
 *      with the following state transitions:
 *
 *        State 0 -> State 1
 *        State 1 -> State 2 if state 1's internal variable indicates "even"
 *        State 1 -> State 3 if state 1's internal variable indicates "odd"
 *        State 2 -> State 0
 *        State 3 -> State 0
 *    
 *      And, of course, one thread implementing each state, sharing the
 *      same state variable and condition variable for notification of
 *      change in the state variable.
 *
 *      Note: Error checking has been left out in much of this example
 *      to increase readability.  Production code should not leave out
 *      this error checking.
 *
 */

#include <stdio.h>
#include <unistd.h>
#include <sys/neutrino.h>
#include <pthread.h>
#include <sched.h>
#include <errno.h>
#include <string.h>
#include <stdlib.h>

/*
 *  our global variables.
 */

volatile int state; // which state we are in

/*
 *  our mutex and condition variable
 */

pthread_mutex_t mutex;
pthread_cond_t cond;

void *state_0(void *);
void *state_1(void *);

int main()
{
	int ret;

	ret = pthread_mutex_init(&mutex, NULL );
	if (ret != EOK)
	{
		fprintf(stderr, "pthread_mutex_init failed: %s\n", strerror(ret));
		exit(EXIT_FAILURE);
	}

	ret = pthread_cond_init(&cond, NULL );
	if (ret != EOK)
	{
		fprintf(stderr, "pthread_cond_init failed: %s\n", strerror(ret));
		exit(EXIT_FAILURE);
	}

	state = 0;

	pthread_create(NULL, NULL, state_1, NULL);
	pthread_create(NULL, NULL, state_0, NULL);

	sleep(20); // let the threads run
	printf("main, exiting\n");
	return 0;
}

/*
 *  state 0 handler (was producer)
 */

void *
state_0(void *arg)
{
	while (1)
	{
		pthread_mutex_lock(&mutex);
		while (state != 0)
		{
			pthread_cond_wait(&cond, &mutex);
		}
		printf("transit 0 -> 1\n");
		state = 1;
		pthread_cond_signal(&cond);
		pthread_mutex_unlock(&mutex);
		/* don't chew all the CPU time */
		delay(100);
	}
	return (NULL);
}

/*
 *  state 1 handler (was consumer)
 */

void *
state_1(void *arg)
{
	while (1)
	{
		pthread_mutex_lock(&mutex);
		while (state != 1)
		{
			pthread_cond_wait(&cond, &mutex);
		}
		printf("transit 1 -> 0\n");
		state = 0;
		pthread_cond_signal(&cond);
		pthread_mutex_unlock(&mutex);
	}
	return (NULL);
}

```
---
## Expected Outcome  

The program should exhibit orderly transitions among four states, controlled by a single condition variable and a shared counter.  
Each thread must execute only when its corresponding state becomes active, demonstrating proper condition-variable-based synchronization.

---

## Program

---

## Output

---

## Result

---


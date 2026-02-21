# EXPERIMENT 202


## Experiment Title  
**Synchronization Using Mutexes in QNX**

---

## Aim/Objective  
To study race conditions in multithreaded programs and resolve them using mutex locks to control access to critical sections.

---

## Problem Statement  

Two programs are provided:

- `nomutex.c`
- `mutex_sync.c`

The program `nomutex.c` creates multiple threads that access shared resources without synchronization, which may result in inconsistent output.

You are required to observe this behavior and then modify `mutex_sync.c` to correct the problem by introducing proper mutex-based synchronization.

---

## Tasks to be Performed/Algorithm  

1. Inspect the file `nomutex.c`.  
2. Run the program with the default number of threads.  
3. Modify the constant `NUMTHREADS` to `4`.  
4. Rebuild and run the program again.  
5. Observe and record the changes in behavior, especially unequal or interleaved `printf()` outputs.  
6. Open the file `mutex_sync.c`.  
7. Add a mutex to protect the critical sections.  
8. Insert lock and unlock operations at appropriate locations in the code.  
9. Ensure that the mutex is not held longer than necessary.  
10. Rebuild and execute the corrected program.  
11. Compare the output with the unsynchronized version.  
12. Analyze how mutex usage affects performance.
---
#### nomutex.c
```c
/*
 *  nomutex.c
 *
 *  This code demonstrates the need for mutual exclusion, by
 *  creating a number of threads that all access the same global
 *  variables.
 *
 */

#include <stdio.h>
#include <sys/neutrino.h>
#include <pthread.h>
#include <sched.h>
#include <atomic.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <errno.h>

/*
 *  The number of threads that we want to have running
 *  simultaneously.
 */

#define NUMTHREADS      1       // try with one thread, then with 4 to show problem
/*
 *  the global variables that the threads compete for.
 *  To demonstrate contention, there are two variables
 *  that have to be updated "atomically".  With RR
 *  scheduling, there is a possibility that one thread
 *  will update one of the variables, and get preempted
 *  by another thread, which will update both.  When our
 *  original thread runs again, it will continue the
 *  update, and discover that the variables are out of
 *  sync.
 *
 *      Note: Error checking has been left out in much of this example
 *      to increase readability.  Production code should not leave out
 *      this error checking.
 */

volatile unsigned var1;
volatile unsigned var2;

void *update_thread(void *);

volatile int done;

int main()
{
	int ret;

	pthread_t threadID[NUMTHREADS]; // a place to hold the thread ID's
	pthread_attr_t attrib; // scheduling attributes
	struct sched_param param; // for setting priority
	int i, policy;

	var1 = var2 = 0; /* initialize to known values */

	printf("mutex_sync:  starting; creating threads\n");

	/*
	 *  we want to create the new threads using Round Robin
	 *  scheduling, and a lowered priority, so set up a thread
	 *  attributes structure.  We use a lower priority since these
	 *  threads will be hogging the CPU
	 */

	ret = pthread_getschedparam(pthread_self(), &policy, &param);
	if ( ret != EOK )
	{
		fprintf(stderr, "pthread_getschedparam failed: %s\n", strerror(ret));
		exit(EXIT_FAILURE);
	}

	ret = pthread_attr_init(&attrib);
	if ( ret != EOK )
	{
		fprintf(stderr, "pthread_attr_init failed: %s\n", strerror(ret));
		exit(EXIT_FAILURE);
	}
	ret = pthread_attr_setinheritsched(&attrib, PTHREAD_EXPLICIT_SCHED);
	if ( ret != EOK )
	{
		fprintf(stderr, "pthread_attr_setinheritsched failed: %s\n", strerror(ret));
		exit(EXIT_FAILURE);
	}
	ret = pthread_attr_setschedpolicy(&attrib, SCHED_RR);
	if ( ret != EOK )
	{
		fprintf(stderr, "pthread_attr_setschedpolicy failed: %s\n", strerror(ret));
		exit(EXIT_FAILURE);
	}
	param.sched_priority -= 2; // drop priority a couple levels
	ret = pthread_attr_setschedparam(&attrib, &param);
	if ( ret != EOK )
	{
		fprintf(stderr, "pthread_attr_setschedparam failed: %s\n", strerror(ret));
		exit(EXIT_FAILURE);
	}
	/*
	 *  create the threads.  As soon as each pthread_create
	 *  call is done, the thread has been started.
	 */

	for (i = 0; i < NUMTHREADS; i++)
	{
		ret = pthread_create(&threadID[i], &attrib, &update_thread, 0);
		if ( ret != EOK )
		{
			fprintf(stderr, "pthread_create failed: %s\n", strerror(ret));
			exit(EXIT_FAILURE);
		}
	}

	/*
	 *  let the other threads run for a while
	 */

	sleep(15);

    /*
     * Tell the threads to exit.
     */

    done = 1;

    // wait for them to exit
    for (i = 0; i < NUMTHREADS; i++)
    {
        ret = pthread_join(threadID[i], NULL);
        if ( ret != EOK )
        {
            fprintf(stderr, "pthread_join failed: %s\n", strerror(ret));
            exit(EXIT_FAILURE);
        }
    }

	printf("all done, var1 is %u, var2 is %u\n", var1, var2);

	return EXIT_SUCCESS;
}

/*
 *  the actual thread.
 *
 *  The thread ensures that var1 == var2.  If this is not the
 *  case, the thread sets var1 = var2, and prints a message.
 *
 *  Var1 and Var2 are incremented.
 *
 *  Looking at the source, if there were no "synchronization" problems,
 *  then var1 would always be equal to var2.  Run this program and see
 *  what the actual result is...
 */

void do_work()
{
	static volatile unsigned var3;

	var3++;
	/* For faster/slower processors, may need to tune this program by
	 * modifying the frequency of this printf -- add/remove a 0
	 */
	if (!(var3 % 10000000))
		printf("thread %d did some work\n", pthread_self());
}

void *
update_thread(void *i)
{
	while (!done)
	{
		if (var1 != var2)
		{
			printf("thread %d, var1 (%u) is not equal to var2 (%u)!\n", pthread_self(), var1, var2);
			var1 = var2;
		}

		/* do some work here */
		do_work();

		var1 += 2;
		var1--;
		var2 += 2;
		var2--;
	}
	return (NULL);
}
```
#### mutex_sync.c
```c
/*
 *  mutex_sync.c
 *
 *  This code is the same as nomutex.c.
 *
 *  The exercise is to use the mutex construct that we learned
 *  about to modify the source to prevent our access problem.
 *
 */

#include <stdio.h>
#include <sys/neutrino.h>
#include <pthread.h>
#include <sched.h>
#include <atomic.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <errno.h>

/*
 *  The number of threads that we want to have running
 *  simultaneously.
 */

#define NUMTHREADS      4

/*
 *  the global variables that the threads compete for.
 *  To demonstrate contention, there are two variables
 *  that have to be updated "atomically".  With RR
 *  scheduling, there is a possibility that one thread
 *  will update one of the variables, and get preempted
 *  by another thread, which will update both.  When our
 *  original thread runs again, it will continue the
 *  update, and discover that the variables are out of
 *  sync.
 *
 *      Note: Error checking has been left out in much of this example
 *      to increase readability.  Production code should not leave out
 *      this error checking.
 */

volatile unsigned var1;
volatile unsigned var2;

void *update_thread(void *);

volatile int done;

int main()
{
	int ret;

	pthread_t threadID[NUMTHREADS]; // a place to hold the thread ID's
	pthread_attr_t attrib; // scheduling attributes
	struct sched_param param; // for setting priority
	int i, policy;

	var1 = var2 = 0; /* initialize to known values */

	printf("mutex_sync:  starting; creating threads\n");

	/*
	 *  we want to create the new threads using Round Robin
	 *  scheduling, and a lowered priority, so set up a thread
	 *  attributes structure.  We use a lower priority since these
	 *  threads will be hogging the CPU
	 */

	ret = pthread_getschedparam(pthread_self(), &policy, &param);
	if ( ret != EOK )
	{
		fprintf(stderr, "pthread_getschedparam failed: %s\n", strerror(ret));
		exit(EXIT_FAILURE);
	}

	ret = pthread_attr_init(&attrib);
	if ( ret != EOK )
	{
		fprintf(stderr, "pthread_attr_init failed: %s\n", strerror(ret));
		exit(EXIT_FAILURE);
	}
	ret = pthread_attr_setinheritsched(&attrib, PTHREAD_EXPLICIT_SCHED);
	if ( ret != EOK )
	{
		fprintf(stderr, "pthread_attr_setinheritsched failed: %s\n", strerror(ret));
		exit(EXIT_FAILURE);
	}
	ret = pthread_attr_setschedpolicy(&attrib, SCHED_RR);
	if ( ret != EOK )
	{
		fprintf(stderr, "pthread_attr_setschedpolicy failed: %s\n", strerror(ret));
		exit(EXIT_FAILURE);
	}
	param.sched_priority -= 2; // drop priority a couple levels
	ret = pthread_attr_setschedparam(&attrib, &param);
	if ( ret != EOK )
	{
		fprintf(stderr, "pthread_attr_setschedparam failed: %s\n", strerror(ret));
		exit(EXIT_FAILURE);
	}
	/*
	 *  create the threads.  As soon as each pthread_create
	 *  call is done, the thread has been started.
	 */

	for (i = 0; i < NUMTHREADS; i++)
	{
		ret = pthread_create(&threadID[i], &attrib, &update_thread, 0);
		if ( ret != EOK )
		{
			fprintf(stderr, "pthread_create failed: %s\n", strerror(ret));
			exit(EXIT_FAILURE);
		}
	}

	/*
	 *  let the other threads run for a while
	 */

	sleep(15);

    /*
     * Tell the threads to exit.
     */

    done = 1;

    // wait for them to exit
    for (i = 0; i < NUMTHREADS; i++)
    {
        ret = pthread_join(threadID[i], NULL);
        if ( ret != EOK )
        {
            fprintf(stderr, "pthread_join failed: %s\n", strerror(ret));
            exit(EXIT_FAILURE);
        }
    }

	// all other threads are gone, so no need to lock var_mutex for var1 and var2 any longer
	printf("all done, var1 is %u, var2 is %u\n", var1, var2);

	return EXIT_SUCCESS;
}

/*
 *  the actual thread.
 *
 *  The thread ensures that var1 == var2.  If this is not the
 *  case, the thread sets var1 = var2, and prints a message.
 *
 *  Var1 and Var2 are incremented.
 *
 *  Looking at the source, if there were no "synchronization" problems,
 *  then var1 would always be equal to var2.  Run this program and see
 *  what the actual result is...
 */

void do_work()
{
	static volatile unsigned var3;

	var3++;
	/* For faster/slower processors, may need to tune this program by
	 * modifying the frequency of this printf -- add/remove a 0
	 */
	if (!(var3 % 10000000))
		printf("thread %d did some work\n", pthread_self());
}

void *
update_thread(void *i)
{
	while (!done)
	{
		if (var1 != var2)
		{
			printf("thread %d, var1 (%u) is not equal to var2 (%u)!\n", pthread_self(), var1, var2);
			var1 = var2;
		}

		/* do some work here */
		do_work();

		var1 += 2;
		var1--;
		var2 += 2;
		var2--;
	}
	return (NULL);
}

```


## Expected Outcome/Sample Output  

The unsynchronized program should display inconsistent or interleaved output due to race conditions.  
After introducing a mutex, the output should become orderly and consistent, demonstrating correct synchronization.  
Students should observe the impact of locking on execution time and responsiveness.

---

## Program

---

## Output

---

## Result

---

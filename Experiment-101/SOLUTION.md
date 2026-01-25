# QNX-Experiment-1
Write a program that creates multiple child processes using fork() and print different messages  in parent and child process. Terminate the parent after 5 seconds and print the pid from the child.
```C
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>

#define NUM_CHILDREN 3

int main(void)
{
    pid_t pid;

    printf("Parent started. PID = %d\n", getpid());

    /* Create multiple children */
    for (int i = 0; i < NUM_CHILDREN; i++) {

        pid = fork();

        if (pid < 0) {
            perror("fork failed");
            exit(EXIT_FAILURE);
        }

        /* Child process */
        if (pid == 0) {

            printf("Child %d started. PID = %d, Parent PID = %d\n",
                   i + 1, getpid(), getppid());

            /* Wait until parent terminates */
            sleep(6);

            printf("Child %d running after parent exit. My PID = %d, New Parent PID = %d\n",
                   i + 1, getpid(), getppid());

            exit(EXIT_SUCCESS);
        }

        /* Parent continues loop to create next child */
    }

    /* Parent process */
    printf("Parent sleeping for 5 seconds...\n");
    sleep(5);

    printf("Parent exiting now. PID = %d\n", getpid());
    exit(EXIT_SUCCESS);
}
```

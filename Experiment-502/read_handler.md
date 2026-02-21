# EXPERIMENT-502

## Experiment Title

**Implementing Data Return in Resource Manager Read Handler in QNX (Momentics IDE)**

---

## Objective

To modify the `io_read()` handler of a QNX resource manager so that the device returns user-defined data when read.

---

## Problem Statement

In the `rm_intro` project, the file `example.c` implements a basic resource manager for the device `/dev/example`.

Currently, the `io_read()` handler returns **zero bytes**, indicating End-of-File (EOF).

Learners must modify the read handler to:

* Generate and return some data (user-defined content).
* Ensure the data is delivered to the client when the device is accessed using standard file operations.
* Verify the functionality using command-line utilities.

---

## Tasks to be Performed

1. Open the **rm_intro** project in QNX Momentics IDE.
2. Locate and open the file `example.c`.
3. Identify the `io_read()` function.
4. Modify the read handler to:

   * create a buffer containing some sample text/data
   * send the data to the client instead of returning zero bytes
   * ensure the number of bytes returned matches the data length
5. Retain verification steps using `iofunc_read_verify()`.
6. Ensure proper handling of read size and offset if required.
7. Build the project in Momentics.
8. Run the program in verbose mode.
9. Test the device using shell commands and observe the returned data.

---

## Build / Run / Test Steps

1. Right-click the application binary → **Run As → QNX C/C++ Application**.

2. Run the program with verbose option:

   ```
   example -v
   ```

3. From the target terminal, execute:

   ```
   cat /dev/example
   ```

4. Verify that:

   * The command prints the user-defined data to the console.
   * The program displays a message indicating execution of `io_read`.
   * No errors occur during read operations.

---
#### exapmple.c
```c
/*
 *  example.c
 *
 *  This module contains the source code for the /dev/example device.
 *
*/

#include <errno.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/iofunc.h>
#include <sys/dispatch.h>
#include <sys/neutrino.h>
#include <sys/resmgr.h>
#include <secpol/secpol.h>

/* default name for this device: /dev/example */

#define EXAMPLE_NAME "/dev/example"

void options (int argc, char *argv[]);

/*
 *  these prototypes are needed since we are using their names in main ()
*/

int io_open (resmgr_context_t *ctp, io_open_t  *msg, RESMGR_HANDLE_T *handle, void *extra);
int io_read (resmgr_context_t *ctp, io_read_t  *msg, RESMGR_OCB_T *ocb);
int io_write(resmgr_context_t *ctp, io_write_t *msg, RESMGR_OCB_T *ocb);

/*
 *  our connect and I/O functions
*/

resmgr_connect_funcs_t  connect_funcs;
resmgr_io_funcs_t       io_funcs;

/*
 *  our dispatch and iofunc variables
*/

dispatch_t              *dpp;
dispatch_context_t      *ctp;
iofunc_attr_t           ioattr;

int     optv;                               // -v for verbose operation

int main (int argc, char *argv[])
{
	int ret;

    printf ("example: starting...\n");

    options (argc, argv);

    /*
     * allocate and initialize a dispatch structure for use by our
     * resource manager framework
    */

    dpp = dispatch_create_channel(-1, DISPATCH_FLAG_NOLOCK);
    if (dpp == NULL) {
        perror("dispatch_create_channel");
        exit (EXIT_FAILURE);
    }

    /*
     * TODO:
     * initialize the connect functions and I/O functions tables to
     * their defaults by calling iofunc_func_init().
     *
     * connect_funcs, and io_funcs variables are already declared.
     *
    */

    /* TODO:
     * over-ride the connect_funcs handler for open with our io_open,
     * and over-ride the io_funcs handlers for read and write with our
     * io_read and io_write handlers
     */


    /* initialize our device description structure
     */

    iofunc_attr_init (&ioattr, S_IFCHR | 0666, NULL, NULL);

    /* TODO:
     *  call secpol_resmgr_attach to register our prefix with the
     *  process manager, and register our connect and I/O
     *  functions with the resmgr library.
     *
    */

    if (ret == -1) {
        perror("secpol_resmgr_attach");
        exit (EXIT_FAILURE);
    }

    /*
     * Allocate the dispatch context for this resource manager,
     * this is the receive-loop data.
     */

    ctp = dispatch_context_alloc (dpp);
    if (ctp == NULL)
    {
    	perror ("dispatch_context_alloc");
    	exit (EXIT_FAILURE);
    }

    /* Transition from initialization to operational process type id */
    secpol_transition_type (NULL, NULL, 0);

    /* our main operation loop */
    while (1) {
    	/* block waiting for client requests */
        if (dispatch_block (ctp) == NULL) {
            perror("dispatch_block");
            exit (EXIT_FAILURE);
        }

        // for debug purposes it can be useful to look at every message & pulse received
        if (optv > 1) {
                if( ctp->message_context.rcvid == 0 ) {
                    printf("pulse code: %d\n", ctp->message_context.msg->pulse.code );
                } else {
                    printf("message type: %d\n", ctp->message_context.msg->type );
                }
        }

        /* handle client requests, calling out into our handlers where appropriate */
        dispatch_handler (ctp);
    }
}

/*
 *  io_open
 *
 *  We are called here when the client does an open.
 *  Do the default open handling and, if verbose, print.
 *
*/

int
io_open (resmgr_context_t *ctp, io_open_t *msg, RESMGR_HANDLE_T *handle, void *extra)
{
    if (optv) {
        printf ("in io_open\n");
    }

    return iofunc_open_default (ctp, msg, handle, extra);
}

/*
 *  io_read
 *
 *  At this point, the client has called their library "read"
 *  function, and expects zero or more bytes.  Currently our
 *  /dev/example resource manager returns zero bytes to
 *  indicate EOF -- no more bytes expected.
 *
 *  After our exercises, it will return some data.
*/

int
io_read (resmgr_context_t *ctp, io_read_t *msg, RESMGR_OCB_T *ocb)
{
    int status;
	
    if (optv) {
        printf ("in io_read\n");
    }

	if ((status = iofunc_read_verify(ctp, msg, ocb, NULL)) != EOK)
        return status;

    // No special xtypes
    if ((msg->i.xtype & _IO_XTYPE_MASK) != _IO_XTYPE_NONE) {
        return ENOSYS;   // causes MsgError( ctp->rcvid, ENOSYS );
    }

    // reply with 0 bytes, that is EOF
    MsgReply(ctp->rcvid, 0, NULL, 0);

	// mark access time for update if any data was read
    if (msg->i.nbytes > 0) {
    	ocb->attr->flags |= IOFUNC_ATTR_ATIME;
    }

    return _RESMGR_NOREPLY;
}

/*
 *  io_write
 *
 *  At this point, the client has called their library "write"
 *  function, and expects that our resource manager will write
 *  the number of bytes that they have specified to some device.
 *
 *  Currently, for /dev/example, all of the clients writes always
 *  work -- they just go into Deep Outer Space.
 *
 *  After our updates, they will be displayed on standard out.
*/

int
io_write (resmgr_context_t *ctp, io_write_t *msg, RESMGR_OCB_T *ocb)
{
    int status;

    if (optv) {
        printf ("in io_write\n");
    }

    if ((status = iofunc_write_verify(ctp, msg, ocb, NULL)) != EOK) {
        return status;
    }

    // No special xtypes
    if ((msg->i.xtype & _IO_XTYPE_MASK) != _IO_XTYPE_NONE) {
        return ENOSYS;
    }

	/* tell the client they wrote all the bytes they tried to write */
	MsgReply(ctp->rcvid, msg->i.nbytes, NULL, 0);

	// if we actually handled any data, mark that a write was done for
	// time updates (POSIX stuff)
    if (msg->i.nbytes > 0) {
    	ocb->attr->flags |= IOFUNC_ATTR_MTIME | IOFUNC_ATTR_CTIME;
    }

    return _RESMGR_NOREPLY;
}

/*
 *  options
 *
 *  This routine handles the command line options.
 *  For our simple /dev/example, we support:
 *      -v      verbose operation
*/

void
options (int argc, char *argv[])
{
    int     opt;

    optv = 0;

    while ((opt = getopt (argc, argv, "v")) != -1) {
        switch (opt) {
        case 'v':
            optv++;
            break;
        }
    }

}

```
---

## Expected Outcome

* The resource manager successfully returns custom data when the device is read.
* The command `cat /dev/example` displays the configured output.
* Console messages confirm that the `io_read()` handler is invoked.
* The device behaves like a readable character device.

---

## Program

---

## Output

---

## Result

---


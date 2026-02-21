# EXPERIMENT 301

## Experiment Title  
**Implementation of Basic Send–Receive–Reply IPC Mechanism in QNX**

---

## Objective  
To implement and test inter-process communication using QNX message passing by completing a checksum server and client program that exchange data using Send, Receive, and Reply primitives.

---

## Problem Statement  

In this project, two source files are provided:

- `server.c`
- `client.c`

The server functions as a **checksum server** with the following operation:

- The client sends a string to the server.  
- The server receives the message.  
- The server computes a checksum for the received string.  
- The server replies to the client with the calculated checksum.

Both the client and server programs are incomplete. You must carefully trace through the source code and identify comments that indicate where the required logic must be implemented.

---

## Tasks to be Performed  

1. Examine `server.c` and `client.c` and locate the sections marked with comments where code must be added.  
2. Complete the missing portions of both programs.  
3. Compile the client and server applications.  
4. Execute the server program and record its **Process ID (PID)** and **Channel ID (CHID)**.  
5. Run the client program using the recorded PID and CHID along with a user-defined text string as command-line arguments.  
6. Observe the output displayed by both the client and the server.  
7. Verify that the checksum sent by the server matches the input string.

---
#### client.c
```c
////////////////////////////////////////////////////////////////////////////////
// client.c
//
// A QNX msg passing client.  It's purpose is to send a string of text to a
// server.  The server calculates a checksum and replies back with it.  The client
// expects the reply to come back as an int
//
// This program program must be started with commandline args.  
// See  if(argc != 4) below
//
// To complete the exercise, put in the code, as explained in the comments below
// Look up function arguments in the course book or the QNX documentation.
////////////////////////////////////////////////////////////////////////////////

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/neutrino.h>
#include "msg_def.h"

int main(int argc, char* argv[])
{
	int coid; //Connection ID to server
	cksum_msg_t msg;
	int incoming_checksum; //space for server's reply
	int status; //status return value used for MsgSend
	int server_pid; //server's process ID
	int server_chid; //server's channel ID

	if (argc != 4)
	{
		printf("ERROR: This program must be started with commandline arguments, for example:\n\n");
		printf("   client 482834 1 abcdefghi    \n\n");
		printf(" 1st arg(482834): server's pid\n");
		printf(" 2nd arg(1): server's chid\n");
		printf(" 3rd arg(abcdefghi): string to send to server\n"); //to make it 
		//easy, let's not bother handling spaces
		exit(EXIT_FAILURE);
	}

	server_pid = atoi(argv[1]);
	server_chid = atoi(argv[2]);

	printf("attempting to establish connection with server pid: %d, chid %d\n", server_pid,
			server_chid);

	//PUT CODE HERE to establish a connection to the server's channel, store the
	//connection id in the variable 'coid'

	if (coid == -1)
	{ //was there an error attaching to server?
		perror("ConnectAttach"); //look up error code and print
		exit(EXIT_FAILURE);
	}

	msg.msg_type = CKSUM_MSG_TYPE;
	strlcpy(msg.string_to_cksum, argv[3], sizeof(msg.string_to_cksum));
	printf("Sending string: %s\n", msg.string_to_cksum);

	//PUT CODE HERE to send message to server and get the reply

	if (status == -1)
	{ //was there an error sending to server?
		perror("MsgSend");
		exit(EXIT_FAILURE);
	}

	printf("received checksum=%d from server\n", incoming_checksum);
	printf("MsgSend return status: %d\n", status);

	return EXIT_SUCCESS;
}
```

#### server.c

```c
////////////////////////////////////////////////////////////////////////////////
// server.c
//
// A QNX msg passing server.  It should receive a string from a client,
// calculate a checksum on it, and reply back the client with the checksum.
//
// The server prints out its pid and chid so that the client can be made aware
// of them.
//
// Using the comments below, put code in to complete the program.  Look up
// function arguments in the course book or the QNX documentation.
////////////////////////////////////////////////////////////////////////////////

#include <stdio.h>
#include <stdlib.h>
#include <errno.h>
#include <sys/neutrino.h>
#include <process.h>

#include "msg_def.h"  //layout of msg's should be defined by a struct, here's its definition

int
calculate_checksum(char *text);

int main(void)
{
	int chid;
	int pid;
	rcvid_t rcvid;
	cksum_msg_t msg;
	int status;
	int checksum;

	//PUT CODE HERE to create a channel, store channel id in the chid variable
	if (chid == -1)
	{ //was there an error creating the channel?
		perror("ChannelCreate()"); //look up the errno code and print
		exit(EXIT_FAILURE);
	}

	pid = getpid(); //get our own pid
	printf("Server's pid: %d, chid: %d\n", pid, chid); //print our pid/chid so
	//client can be told where to connect

	while (1)
	{
		//PUT CODE HERE to receive msg from client

		if (rcvid == -1)
		{ //was there an error receiving msg?
			perror("MsgReceive"); //look up errno code and print
			exit(EXIT_FAILURE); //give up
		}

		//PUT CODE HERE to calculate the check sum by calling calculate_checksum()

		//PUT CODE HERE TO reply to client with checksum, store the return status in status

		if (status == -1)
		{
			perror("MsgReply");
		}
	}
	return 0;
}

int calculate_checksum(char *text)
{
	char *c;
	int cksum = 0;

	for (c = text; *c != '\0'; c++)
		cksum += *c;
	return cksum;
}
```

## Expected Outcome  

The client should successfully send a string to the server, and the server should return the correct checksum value. Proper IPC communication using Send–Receive–Reply primitives must be demonstrated.

---

## Program

---

## Output

---

## Result

---


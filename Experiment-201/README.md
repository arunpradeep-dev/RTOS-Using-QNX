# EXPERIMENT 02

## Experiment Title  
**Implementation of Basic Send–Receive–Reply IPC Mechanism in QNX**

---

## Objective  
To implement and test inter-process communication using QNX message passing by completing a checksum server and client program that exchange data using Send, Receive, and Reply primitives.

---

## Problem Statement  

In the given IPC project, two source files are provided:

- `server.c`
- `client.c`

The server functions as a **checksum server** with the following operation:

- The client sends a string to the server.  
- The server receives the message.  
- The server computes a checksum for the received string.  
- The server replies to the client with the calculated checksum.

Both the client and server programs are incomplete. Students must carefully trace through the source code and identify comments that indicate where the required logic must be implemented.

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

## Expected Outcome  

The client should successfully send a string to the server, and the server should return the correct checksum value. Proper IPC communication using Send–Receive–Reply primitives must be demonstrated.

---


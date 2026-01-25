# EXPERIMENT-302

## Experiment Title  
**Pulse-Based Event Notification Using QNX IPC (Momentics IDE)**

---

## Objective  
To implement pulse-based event delivery between two processes in QNX by completing client and server programs that register, store, and periodically deliver events using the microkernel’s event and pulse mechanisms.

---

## Problem Statement  

In the `ipc` project, two source files are provided:

- `event_server.c`
- `event_client.c`

Complete these programs so that:

- `event_client` creates and registers an event containing a **pulse**.
- `event_server` receives the event, verifies it, and stores the received event and RCVID.
- `event_server` delivers the pulse event to the client **every 1 second**.
- `event_client` receives and prints the pulse **every 1 second**.

`TODO` comments are provided in the code to indicate where changes are required.

---

## Tasks to be Performed  

### Part A – Modify `event_client.c`
1. Open `event_client.c` in Momentics.
2. Search for `TODO` and locate missing sections.
3. Add code to:
   - format the event (pulse event structure)
   - register the event with the server

### Part B – Modify `event_server.c`
4. Open `event_server.c` in Momentics.
5. Search for `TODO` and locate missing sections.
6. Add code to:
   - verify the received event
   - store the event and the RCVID
   - deliver the event once every 1 second

---

## Build Steps (Momentics IDE)

1. Open the project `ipc` in Momentics.
2. Ensure both source files are included in the project build.
3. Build the project:
   - **Project → Build Project** (or Hammer icon)
4. Confirm there are no compilation errors.

---

## Run / Test Steps (Momentics IDE)

### Run the Server
1. Right-click `event_server` (binary) → **Run As → QNX C/C++ Application**
2. Keep the server running and note its console output.

### Run the Client
3. Right-click `event_client` (binary) → **Run As → QNX C/C++ Application**
4. Observe the output in both consoles.

---

## Verification Checklist

- Every second:
  - `event_server` prints that it **sent a pulse**
  - `event_client` prints that it **received a pulse**
- Terminate the client and restart it:
  - Kill/stop `event_client`
  - Run `event_client` again
- Observe correct re-registration and continued pulse delivery.

---

## Expected Outcome  

- The server periodically delivers a pulse event every 1 second.
- The client receives a pulse every 1 second and prints confirmation.
- Restarting the client demonstrates correct event registration and handling.
- The implementation uses `TODO` locations to add correct event setup, verification, storage, and delivery logic.

---

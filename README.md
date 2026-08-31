# Modular Process Control
This project presents a PLC software module designed to control different processes using the same controller.
This enables a modular architecture for PLC applications by applying Object‑Oriented principles within
the IEC 61131‑3 programming ecosystem.

One of the advantages of this design is the possibility of reusing a significant portion of the visualization/HMI
for completely different applications, since the control module can be utilized for processes that do not necessarily
require a radically different control interface.

The program is implemented in CODESYS, but the design applies to any other programming environment.

## Example Process (A Pick‑and‑Place Robot Arm)
The controller can be used in highly heterogeneous applications. In this example, a robotic arm simulation is used
to provide a complete demonstration program for the PLC.

## Overview of the Main Software Components
- **Main Program**: Implemented in Ladder to provide a clear overview of the system.

- **Controller**: Inspired by the PackML machine model and implemented in SFC. It can be further extended to provide
additional functionality without modifying the process, and vice‑versa.

- **Process**: A simulation of a pick‑and‑place robotic arm. It is implemented in SFC with a parallel simulator step
that executes an action in Structured Text to simulate waiting times between movement operations and coordinate the
process steps.

- **Signal Handler**: Implemented in FBD to coordinate abort signals and communication between the process and the controller.

- **Visualization**: Provided to test the program. It contains start, stop, reset, e‑stop, status lamps, a fault button
to trigger an abort for testing, and several buttons for the robot.

- **SCADA**: An Ignition‑based control panel with a SQL Server historian and alarm journal.

The main program also contains logic for the visualization lamps.

## Detailed Description
The system architecture is inspired by the Model‑View‑Controller Object‑Oriented pattern to ensure that modules are
decoupled and reusable. Specifically:

The View corresponds to the SCADA and the visualization.

The Controller corresponds directly to the control module.

The Model corresponds to the process.

The behavior of the example robot is as follows:

### Main Cycle
When the robot starts, it moves to the initial pose and then waits for activation of either sensor A or sensor B,
corresponding to two different products. When either sensor is activated, the robot performs the pick‑and‑place
operation and returns to the initial position to wait for the next activation.

This can be done in manual mode using the visualization buttons or in auto mode, which can be set from the SCADA.

If the system is stopped before a cycle is finished, it will allow the current cycle to complete before stopping.

https://github.com/user-attachments/assets/989aed4c-790c-4969-bb02-78cff1bb913d



### Reset Function
When in Idle or Stopped, pressing Reset will move the robot to the Home position before starting.

https://github.com/user-attachments/assets/5300572b-04a2-4b07-9fe8-a1cedaa3f707



### Robot Timeout
If any movement operation takes longer than expected, a timeout signal is triggered and the controller transitions
to Abort mode. A button to clear the fault is provided in the visualization.

The timeout is defined as a constant in the global scope along with the other movement times. To simulate a
timeout, it can be set to a low value so that the timeout always triggers, as shown in the video.

https://github.com/user-attachments/assets/4f858ff3-7be2-453e-962d-6bc88da29ec1



### System Abort
Fault and e‑stop buttons are provided in the visualization to simulate a fault or emergency stop, which will set
the system to Abort mode. An alarm journal in the SCADA monitors these events.

These are abort signals coming from sources other than the robot. An abort signal will interrupt the ongoing cycle.

https://github.com/user-attachments/assets/5c5d18bc-2076-4512-826a-459ba6219098



### Auto Mode
It is available in the SCADA. It includes a field to select the product and another to select a waiting period
between cycles. When enabled, the system will keep running in a loop by simulating sensor activations for the
selected product.

https://github.com/user-attachments/assets/d5e785cd-c857-444f-80a8-15967128142b


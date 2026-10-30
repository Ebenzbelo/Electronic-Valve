# Electronic-Valve
## Objective 
To create a physical system model suitable for designing a feedback control loop using Simscape Library.
## Overview 
It is expedient to understand how to model how to use a DC motor to turn the handle on a valve, and build a feedback controller around this plant to maintain a desired flow rate through the valve.

The project includes a simplified datasheet that describes the relationship between the valve position and the resulting flow rate. Therefore, the electrical and mechanical portions of the system need to be modelled. The fluid flow for the relationship depicted can be described in the image by using a Simulink lookup table.

<img width="362" height="187" alt="1" src="https://github.com/user-attachments/assets/1b18483a-44d1-4bb4-8aa1-580f216c9d98" />

The electrical portion of the system is the DC motor, as DC motors contain multiple coils of copper wire that interact with permanent magnets to generate motion from electricity. They are often represented by a resistor and inductor in series that represent the internal resistance of the copper wire and the inductance of the coils, respectively. DC motors are typically driven by a variable voltage source. Spinning motors also generate back electromotive force, which adds a load to the system. The image below shows the resulting circuit.

<img width="372" height="243" alt="Schematic Circuit" src="https://github.com/user-attachments/assets/1783d9ff-4586-407a-ab94-10f949ae6ce0" />

The mechanical portion of the system consists of the rotating handle and needle, with a constant friction between the needle and casing. There are also hard stops that limit the rotation of the needle to between 0 and 360 degrees.

<img width="375" height="233" alt="Mechanical Illustration" src="https://github.com/user-attachments/assets/e248108e-7981-4493-9f5e-eea58793a9eb" />

The DC motor is coupled to the valve handle through a set of gears that supply the torque to rotate the handle. To control the flow rate, both the DC motor and the valve, which together make up the plant should be modelled. The plant input is the voltage to the motor, and the output is flow rate. The fluid domain does not need to be included in the model, since the valve position can be measured and then used to calculate flow rate.

<img width="870" height="207" alt="Block Diagram" src="https://github.com/user-attachments/assets/2b7a345b-2029-46b2-8b75-fc1e3691df65" />

## Methodology


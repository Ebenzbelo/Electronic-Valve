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
For this project, the input voltage controls the torque output of a DC motor. As the objective is to use the plant model in a feedback loop, it is necessary to change the voltage using an external signal. The Controlled Voltage Source block (Electrical > Electrical Sources) uses a physical signal input to set the block's voltage.

The DC motor model consists of a voltage source, resistor, and inductor in series with the back electromotive force. A motor converts electrical energy into rotational energy. 
This motor is connected to the valve through a set of gears. The rotating valve components can be modelled as a lumped inertia.

The valve has stops that limit its rotation between 0 degrees, when the valve is fully closed, and 360 degrees, when the valve is fully opened. The Rotational Hard Stop block (Mechanical > Rotational Elements) can be used to model these physical boundaries.

Now, the objective is to control the flow rate through the valve. The relationship between valve position, in degrees and flow rate are considered and therefore the valve position needs to be measured.
The datasheet below describes the linear relationship between valve position and flow rate.

<img width="307" height="151" alt="Table 1 1" src="https://github.com/user-attachments/assets/5c851417-1c9f-4c14-9760-cab73c62f2f1" />

Simulink contains a 1-D Lookup Table block (Simulink > Lookup Tables). This block can be to specify an input-output relationship using a table of values. The block interpolates between the specified values.

When the plant model is created with motor voltage as the input and flow rate as the output, a feedback control loop should be added. Hence, a flow rate of 5 liters per minute can be achieved.

STEP 1: 
A Controlled Voltage Source block and a PS Constant block are added to your model.
The Constant value parameter of the PS Constant block is set to 36 V and connect the block to the Controlled Voltage Source block.

<img width="696" height="621" alt="Step 1" src="https://github.com/user-attachments/assets/bbbaace1-e9d9-4f03-a6dd-97a020a46070" />

A Resistor, Inductor, and Electrical Reference block are added to the model.
In the Resistor block, Resistance parameter is set to 50 Ohm while in the Inductor block, the Inductance parameter is set to 1.2e-4 H.

<img width="810" height="526" alt="Step 1 1" src="https://github.com/user-attachments/assets/c6ec5536-d631-45de-bc1b-905f71f5fafb" />

STEP 2:
A Rotational Electromechanical Converter block is added to your model. The Constant of proportionality K parameter is set to 10 V*s/rad. The connections are made to complete the electrical circuit portion of the model.
Then, the Solver Configuration block is connected to your network.

<img width="835" height="411" alt="Step 2" src="https://github.com/user-attachments/assets/2f5f601d-f262-4055-b109-1e5edbc79cf0" />

STEP 3: 
A Gear Box and an Inertia block are added to the model.
In the Gear Box block, the Gear ratio parameter is set to 5. In the Inertia block, the Inertia is set to 0.1 kg*m^2.
Then, the Rotational Electromechanical Converter and the Inertia blocks are connected to the Gear Box block.
A Mechanical Rotational Reference block is added to ground the C port of the converter block.

<img width="921" height="453" alt="Step 3" src="https://github.com/user-attachments/assets/8b1e6807-c973-42fe-833a-7d79ec3030d2" />

STEP 4:
A Rotational Hard Stop block is added to the model.
The block is connected to the Inertia block and to a Mechanical Rotational Reference block.
The Upper bound parameter is set to 360 deg and the Lower bound parameter to 0 deg.

<img width="984" height="554" alt="Step 4" src="https://github.com/user-attachments/assets/58a193fe-6e50-46ea-8324-357dc5ba3d85" />

STEP 5: 
A Ideal Rotational Motion Sensor block and a PS-Simulink Converter block are added to the model.
The sensor and PS-Simulink Converter block are connected to the rest of the model in order to measure the rotation of the valve.
In the PS-Simulink Converter block, the Output signal unit parameter is set to deg.

<img width="1118" height="600" alt="Step 5" src="https://github.com/user-attachments/assets/9a533fdd-fe8f-48b1-a1d6-84f5f20bb2e0" />

STEP 6:
A 1-D Lookup Table block is added to your model and connected to the angular rotation measurement.
The Value of Table data is set to [0 50].
The Value of Breakpoints 1 is set to [0 360].

Hence, a Scope block can be added and connected to the output of the 1-D Lookup Table block to see the flow rate increase at a constant rate until the valve hits the hard stop.

<img width="1060" height="444" alt="Step 6" src="https://github.com/user-attachments/assets/7680f540-d43b-4060-a759-692c7518d344" /><img width="1680" height="884" alt="Scope" src="https://github.com/user-attachments/assets/30a841ae-224f-4669-8034-549625b9893d" />

STEP 7:
Now, the PS Constant block is deleted and these blocks are added:
Simulink-PS Converter
Constant
Discrete PID Controller
Sum
Unit Delay
Then, they are arranged to create a feedback control loop, with the Constant block providing the reference signal.
The Unit Delay is used to break the algebraic loop.

<img width="1089" height="417" alt="Step 7" src="https://github.com/user-attachments/assets/982296db-e8d2-4e6e-9efb-1f199b62a1a8" />

STEP 8:
In the Constant block, the Constant value parameter is set to 5.
 
In the Discrete PID Controller, the Controller parameter is set to PI and the Sample time parameter to 0.1. In the Controller parameters section, these parameters are changed as:
Proportional (P) 15
Integral (I) 10

<img width="1454" height="590" alt="Step 8" src="https://github.com/user-attachments/assets/13ec1271-3e84-467f-be16-7c3b782fab95" />




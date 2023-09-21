---
layout: default
title: How to connect the IB-0x10 digital input and outputs
parent: How to
nav_order: 5
has_children: false
has_toc: false
---

# How to connect the IB-0x10 digital inputs and outputs

The interface modules IB-0210 and IB-0310 implement 4 Digital Inputs and 4 Digital Outputs. The I/O can be used to interface with standard input and output components like switches and relays or other systems with I/O capabilities like PLCs. The sections below explain the I/O capabilites and a basic example on how to use the I/Os.


## I/O pinout and specification

The implementation of the digital I/Os are the same for the IB-0x10 modules (IB-0310 and IB-0210). The I/O are gavanically isolated from the Blox system and require an external power supply. 
There are 4 digital outputs and 4 digital inputs. Both are arranged on one row of the connector.

<p align="center">
<img src="/assets/images/pages/interface-blox/IB-0310/IB-0x10-IO-Pinout.svg">
</p>


The external power supply for the I/O (VI/O+ and VI/O-) is required to power the galvanic isolated components of the interface module. It has a nominal voltage of 24VDC.  
On both rows of the connector is a VI/O+ and VI/O- pin. Those are inteconnected between the rows. Due to this connection only one external power supply is needed for both inputs and outputs
Next to each I/O there is also a VI/O+ pin to reduce power wiring branches. 

<p align="center">
<img src="/assets/images/pages/interface-blox/IB-0310/IB-0x10-IO-Power-Pinout.svg">
</p>

## Digital Outputs

The digital outputs can be use to drive relays or inputs of other systems. The outputs are open collector and can drive up to 200mA. 

Below is a wiring diagram of how to connect a relay on DO1. A relay will require a flyback diode to protect the output. 

<p align="center">
<img src="/assets/images/pages/interface-blox/IB-0310/IB-0x10-IO-Relay-connection-pinout.svg">
</p>

The diagram below shows the basic block schematic.   

<p align="center">
<img src="/assets/images/pages/interface-blox/IB-0310/IB-0x10-IO-Relay-connection.svg">
</p>


## Digital Inputs

The digital inputs take be used with a switch or an digital output of another system. 

Below is a wiring diagram of how to connect a switch on DI1. 

<p align="center">
<img src="/assets/images/pages/interface-blox/IB-0310/IB-0x10-IO-Input-connection-wiring.svg">
</p>


The diagram below shows the basic block schematic.   

<p align="center">
<img src="/assets/images/pages/interface-blox/IB-0310/IB-0x10-IO-Input-Connection.svg">
</p>








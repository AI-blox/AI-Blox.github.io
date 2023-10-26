---
layout: default
title: How to configure the IB-0x10 digital input and outputs
parent: How to
nav_order: 2
has_children: false
has_toc: false
---

# How to map the IB-0x10 digital inputs and outputs to sysfs

The interface modules IB-0210 and IB-0310 implement 4 Digital Inputs and 4 Digital Outputs. The I/O can be used to interface with standard input and output components like switches and relays or other systems with I/O capabilities like PLCs. The sections below explain the I/O to do the mapping of these I/Os to the Linux sysfs.


## Access to the DIO from the sysfs

sysfs is a pseudo file system in Linux. Through virtual files it exports information of the kernel subsystems, hardware devices and associated device drivers. The exported virtual files are also be used for congifuration. 

The DIO of the IB-0x10 are connected to GPIOs of the Jetson SOM. To make the DIO accessible from software, the DIO will be mapped to its corresponding GPIO. Each GPIO will be exported to a folder with `/sys/class/gpio/`. Each GPIO will have its own folder containing several files to set the direction, set the value and retreive the value. 

Due to the differences between the Jetson Nano and Xavier, each has its own GPIO numbering and are covered seperatly in the sections below. 

## Mapping the DIO on a Blox Nano

The tables shows the IB-0x10 DIO and thier correspinding GPIO on the Jetson Nano SOM.

| **DI** | **GPIO** |
|--------|----------|
|   DI1  |    169   |
|   DI2  |    216   |
|   DI3  |    202   |
|   DI4  |     64   |

```bash
# Configure the DI
# DI1
sudo echo 169 > /sys/class/gpio/export
sudo echo in > /sys/class/gpio/gpio169/direction
# DI2
sudo echo 216 > /sys/class/gpio/export
sudo echo in > /sys/class/gpio/gpio216/direction
# DI3
sudo echo 202 > /sys/class/gpio/export
sudo echo in > /sys/class/gpio/gpio202/direction
# DI4 
sudo echo 64 > /sys/class/gpio/export
sudo echo in > /sys/class/gpio/gpio64/direction
```

| **DO** | **GPIO** |
|--------|----------|
|   DO1  |     62   |
|   DO2  |     66   |
|   DO3  |     65   |
|   DO4  |     63   |

```bash
#  Configure the DO
# DO1
sudo echo 62 > /sys/class/gpio/export
sudo echo out > /sys/class/gpio/gpio62/direction
# DO2
sudo echo 66 > /sys/class/gpio/export
sudo echo out > /sys/class/gpio/gpio66/direction
# DO3 
sudo echo 65 > /sys/class/gpio/export
sudo echo out > /sys/class/gpio/gpio65/direction
# DO4
sudo echo 63 > /sys/class/gpio/export
sudo echo out > /sys/class/gpio/gpio63/direction
```


## Mapping the DIO on a Blox Xavier


| **DI** | **GPIO** |
|--------|----------|
|   DI1  |    417   |
|   DI2  |    436   |
|   DI3  |    418   |
|   DI4  |    TBC   |

```bash
# Configure the DI
# DI1
echo 417 > /sys/class/gpio/export
echo in > /sys/class/gpio/gpio417/direction
# DI2
echo 436 > /sys/class/gpio/export
echo in > /sys/class/gpio/gpio436/direction
# DI3
echo 418 > /sys/class/gpio/export
echo in > /sys/class/gpio/gpio418/direction
# DI4 not configured yet as gpio
```

| **DO** | **GPIO** |
|--------|----------|
|   DO1  |    264   |
|   DO2  |    419   |
|   DO3  |    TBC   |
|   DO4  |    266   |

```bash
#  Configure the DO
# DO1
echo 264 > /sys/class/gpio/export
echo out > /sys/class/gpio/gpio264/direction
#DO2
echo 419 > /sys/class/gpio/export
echo out > /sys/class/gpio/gpio419/direction
#DO3 not configured yet as gpio
#DO4
echo 266 > /sys/class/gpio/export
echo out > /sys/class/gpio/gpio266/direction
```

## Using the DIO

Setting the value of the DO1 on a Jetson Nano with an IB-0x10
```bash
echo 1 | sudo tee /sys/class/gpio/gpio62/value
```

Get the DI1 input value on a Jetson Nano with an IB-0x10

```bash
sudo cat /sys/class/gpio/gpio169/value
```
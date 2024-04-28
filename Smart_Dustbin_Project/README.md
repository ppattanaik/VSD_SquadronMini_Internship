# Smart Dustbin Project
## Introduction
Trash bins, whether termed dustbins, garbage receptacles, or trash cans, serve as essential containers for temporarily housing waste in a variety of settings, including households, workplaces, public spaces, and outdoor areas. They fulfill a vital role in waste management, particularly in regions where littering is prohibited, serving as the primary means for disposing of small waste items. 
Traditionally, waste segregation practices often involve the use of separate bins for wet and dry waste, as well as for recyclable and non-recyclable materials, contributing to efficient waste management and recycling efforts.
## Overview
The Smart Dustbin project showcased here introduces an innovative solution utilizing a VSD Squadron Mini Developement Board, an Ultrasonic Sensor, and a Servo Motor. Its core functionality lies in its ability to autonomously open the lid of the dustbin upon detecting the presence of a human hand.
Central to the project is the principle of object detection. Here, an Ultrasonic Sensor is strategically placed atop the dustbin's lid. When the sensor detects an object, such as a human hand, it triggers the VSD Squadron Mini to activate the Servo Motor, thereby initiating the lid-opening mechanism.
This technology-driven approach offers a hands-free and user-friendly solution for waste disposal, potentially enhancing hygiene and sanitation practices across various settings. Its implementation holds promise for streamlining waste management processes and fostering a cleaner and more sustainable environment.
## Components Required
  - VSD Squadron Mini developement board with CH32V003F4U6 chip with 32-bit RISC-V core based on RV32EC instruction set
  - HC-SR04 Ultrasonic Sensor
  - SG90 Servo Motor
  - External Power Supply
  - Bread Board
  - Jumper Wires
## Circuit Connection Diagram
![PowerPoint Slide Show  -  Presentation1 29-04-2024 03_09_19](https://github.com/ppattanaik/VSD_SquadronMini_Internship/assets/63561037/c2d4213d-435a-4330-ae60-8ba3db8c0dd4)

###  Table for Pin connection
| HC-SR04 Ultrasonic Sensor | VSD Squadron Mini |
| ------------- | ------------- |
| Trigger pin | PD3 |
| Echo pin | PD2 |
| VCC | 5V |
| GND | GND |

| Servo motor(SG90)  | External Power Supply | VSD Squadron Mini |
| ------------- | ------------- |------------- |
| Control pin | - |PD4 |
| VCC  | 6V | - |
| GND | GND | GND |



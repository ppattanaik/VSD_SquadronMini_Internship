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
  - 500RPM Gear Motor
  - L298N Motor Driver Module
  - External Power Supply
  - Bread Board
  - Jumper Wires
## Software Required
  - MounRiver Studio
      - To download the studio [Click Here](http://www.mounriver.com/download)
## Circuit Connection Diagram
<img width="488" alt="PowerPoint Slide Show  -  Presentation1 pptx 5_3_2024 12_29_08 PM" src="https://github.com/ppattanaik/VSD_SquadronMini_Internship/assets/63561037/6cdc1f19-5207-4302-b635-bcbcdc442ea8">

###  Table for Pin connection
| HC-SR04 Ultrasonic Sensor | VSD Squadron Mini |
| ------------- | ------------- |
| Trigger pin | PD3 |
| Echo pin | PD2 |
| VCC | 5V |
| GND | GND |

| Gear Motor  | Motor Driver Module | VSD Squadron Mini |
| ------------- | ------------- |------------- |
| - | Control pin 1 |PD4 |
| - | Control pin 2 |PD6 |
| VCC  | OUT1 | - |
| GND | OUT2 | - |

|  External Power Supply | Motor Driver Module | VSD Squadron Mini |
| ------------- | ------------- |------------- |
| 6V | VCC | - |
| GND | GND | GND |

## Working Code
```

//Including Libraries
#include "debug.h"

//Function to configure GPIO Pins
void GPIO_Toggle_INIT(void)
{
    GPIO_InitTypeDef GPIO_InitStructure = {0};   ////Structure variable used for the GPIO configuration

    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE);  ////To Enable the clock for Port D

    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_6 | GPIO_Pin_4 | GPIO_Pin_3; //// Defining Pins to configure
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;  ///// Setting Pin mode as Output type
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;  ////Defining GPIO Speed   -----NOTE: There are three options: GPIO_Speed_10MHz, GPIO_Speed_2MHz, GPIO_Speed_50MHz
    GPIO_Init(GPIOD, &GPIO_InitStructure);    ////Instantiating the GPIO pins with the structure variable

    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_2;  //// Instantiation of Pins
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPU;  ///// Setting Pin mode as Input type
    GPIO_Init(GPIOD, &GPIO_InitStructure);    ////Instantiating the GPIO pins with the structure variable


}

//Main Function
int main(void)
{
  
    NVIC_PriorityGroupConfig(NVIC_PriorityGroup_1);
    SystemCoreClockUpdate(); //// Configure MCU Clock HSI
    Delay_Init();    ////Dealy for allowing clock to Stabilize

//USART initialization
#if (SDI_PRINT == SDI_PR_OPEN)
    SDI_Printf_Enable();
#else
    USART_Printf_Init(115200);
#endif
    printf("SystemClk:%d\r\n", SystemCoreClock);
    printf( "ChipID:%08x\r\n", DBGMCU_GetCHIPID() );

    //Calling Function to Configure GPIO Pins
    GPIO_Toggle_INIT();

    while(1)
    {
        u8 i = 0, j = 0, k = 0;  //// Declaring local Variables

        GPIO_WriteBit(GPIOD, GPIO_Pin_3, SET);  //// Setting Trigger Pin to send pulses
        Delay_Ms(10);      ///// Pulse Width
        k = GPIO_ReadInputDataBit(GPIOD, GPIO_Pin_2); //// Reading pulse captured by echo pin

        if (k  == 0){

            Delay_Ms(1000);   //// Delay to Synchronise with the Clock

            //Defining condition for Motor Spin Direction
            GPIO_WriteBit(GPIOD, GPIO_Pin_6, (i == 0) ? (i = Bit_SET) : (i = Bit_RESET));
            GPIO_WriteBit(GPIOD, GPIO_Pin_4, (j == 0) ? (j = Bit_RESET) : (j = Bit_SET));
            Delay_Ms(1300);

            //Defining condition to stop Motor
            GPIO_WriteBit(GPIOD, GPIO_Pin_6, (i == 0) ? (i = Bit_SET) : (i = Bit_RESET));
            GPIO_WriteBit(GPIOD, GPIO_Pin_4, (j == 0) ? (j = Bit_RESET) : (j = Bit_SET));
            Delay_Ms(5000);

            //Defining condition for Motor Spin Direction
            GPIO_WriteBit(GPIOD, GPIO_Pin_4, (i == 0) ? (i = Bit_SET) : (i = Bit_RESET));
            GPIO_WriteBit(GPIOD, GPIO_Pin_6, (j == 0) ? (j = Bit_RESET) : (j = Bit_SET));
            Delay_Ms(1300);

            //Defining condition to stop Motor
            GPIO_WriteBit(GPIOD, GPIO_Pin_6, (i == 0) ? (i = Bit_SET) : (i = Bit_RESET));
            GPIO_WriteBit(GPIOD, GPIO_Pin_4, (j == 0) ? (j = Bit_RESET) : (j = Bit_SET));
            Delay_Ms(4000);
        }

        //Conditions to disable motor actuation
        if (k != 0){
            
            Delay_Ms(1000); //// Delay to Synchronise with the Clock

            //Defining condition to stop Motor
            GPIO_WriteBit(GPIOD, GPIO_Pin_4, (i == 0) ? (i = Bit_SET) : (i = Bit_RESET));
            GPIO_WriteBit(GPIOD, GPIO_Pin_6, (j == 0) ? (j = Bit_SET) : (j = Bit_RESET));
            Delay_Ms(4000);

        }

        //Resetting Trigger Pin
        GPIO_WriteBit(GPIOD, GPIO_Pin_3, RESET);
        Delay_Ms(5);

    }
}
```
## Demo Video
https://github.com/ppattanaik/VSD_SquadronMini_Internship/assets/63561037/3dace670-d682-4a39-b12c-b5fa99738c5a

## Hardware Security Testing Using Fault Injection
### Fault Injection
Fault injection is a technique used in embedded systems security testing to evaluate the robustness of a system against various types of faults or attacks by introducing intentional faults to the system.

### Purpose
It helps in identifying vulnerabilities and weaknesses in the system's design and implementation, particularly in security-critical applications.

### Methods of Fault Injection
- Voltage Glitching
  - Description:
    Introduces brief voltage spikes or drops to disrupt the normal operation of the microcontroller.
  - Effect:
    Voltage glitches can cause the microcontroller to execute instructions incorrectly or enter an unexpected state.
- Clock Glitching:
  - Description:
    Manipulates the clock signal supplied to the microcontroller to disrupt its operation.
  - Effect:
    By altering the clock signal, the timing of instructions can be manipulated, potentially causing the microcontroller to malfunction.
- Electromagnetic Interference (EMI):
  - Description:
    External electromagnetic radiation interferes with the microcontroller's operation.
  - Effect:
    EMI can cause the microcontroller to execute instructions incorrectly or enter an unexpected state.
- Faulty Inputs:
  - Description:
    Introduces invalid or unexpected inputs to the microcontroller.
  - Effect:
    Malformed data packets, invalid sensor readings, or unexpected signals can lead to system malfunctions.
- Code Injection:
  - Description:
    Injects malicious code into the microcontroller's memory or execution flow, exploiting vulnerabilities in firmware or application code.
  - Effect:
    Enables unauthorized access, execution of arbitrary commands, data corruption, denial of service, or privilege escalation, posing significant security risks to the          system.
- Peripheral Fault Injection:
  - Description:
    Manipulates peripheral devices connected to the microcontroller to induce faults in their behavior.
  - Effect:
    This can lead to communication errors, sensor inaccuracies, actuator malfunctions, or disruptions in the interaction between the microcontroller and peripherals,            potentially compromising system functionality and reliability.
- JTAG/SWD Manipulation:
  - Description:
    Alters the Joint Test Action Group (JTAG) or Serial Wire Debug (SWD) interface to inject faults directly into the microcontroller's internal debugging and testing 
    mechanisms.
  - Effect:
    May disrupt debugging and testing operations, leading to erroneous diagnosis or inability to identify faults accurately. Additionally, manipulation could potentially 
    compromise system security by allowing unauthorized access or control over the microcontroller's internal functions.
- Injection via System Reset:
  - Description:
    Manipulates the system reset mechanism to induce unexpected resets or faults in the microcontroller's startup or initialization process.
  - Effect:
    Can cause system instability, data loss, or erratic behavior during startup. Additionally, injection of faults during reset may lead to vulnerabilities such as              bypassing security measures or compromising the integrity of the system's boot process.
- Radiation-induced Single Event Effects (SEE):
  - Description:
    Exposes the microcontroller to ionizing radiation, such as cosmic rays or particle radiation, inducing transient faults in its operation.
  - Effect:
    May cause bit flips, memory corruption, or logic errors, leading to system malfunctions, data corruption, or unexpected behavior. SEE events can disrupt critical            operations and compromise system reliability, particularly in environments with high radiation levels such as aerospace or high-altitude applications.
- Quantum-based Fault Injection:
  - Description:
    Exploits quantum effects, like quantum tunneling or radiation-induced soft errors, to induce faults in the microcontroller's operation at the quantum level.
  - Effect:
    Quantum-based fault injection can lead to unpredictable errors, bit flips, or data corruption in the microcontroller's memory or logic circuits. These faults can            compromise system reliability and security, especially in high-performance computing or quantum computing applications where quantum effects play a significant role.
    
### Importance of Fault Injection Testing
- Security Evaluation:
  Identifies vulnerabilities that could be exploited by malicious actors to compromise system security.
- Reliability Assessment:
  Helps in assessing the robustness and reliability of the microcontroller-based system under various fault conditions.
- Compliance Testing:
  Ensures compliance with industry standards and regulations, especially in safety-critical applications.
- Risk Mitigation:
  Enables developers to identify and mitigate potential risks before deployment, reducing the likelihood of system failures or security breaches.
  
### Challenges and Considerations
- Realism of Fault Scenarios:
  Ensuring that fault injection scenarios accurately represent real-world threats and vulnerabilities.
- Resource Constraints:
  Testing methodologies should be efficient and effective, considering the limited resources available in embedded systems.
- Safety Concerns:
  Minimizing the risk of causing permanent damage to the hardware during fault injection experiments.
- Test Coverage:
  Achieving comprehensive test coverage to identify all potential vulnerabilities and weaknesses in the system.
  
### Implementing fault injection in the Smart Dustbin
#### Fault Injection Circuit
![Circuit design Daring Gogo - Tinkercad and 1 more page - Personal - Microsoft​ Edge 5_10_2024 3_51_13 AM](https://github.com/ppattanaik/VSD_SquadronMini_Internship/assets/63561037/afc51a51-8e06-4688-8a71-d4200a5335ad)

#### Demo
https://github.com/ppattanaik/VSD_SquadronMini_Internship/assets/63561037/67adef22-a51e-48b3-a5dc-646c1c3ee85f

#### Description
Here, the PWM pins of arduino along with a potentiometre are used to create a variable voltage suppy for inducing faults into different peripheral components of the Smart Dustbin.
The potentiometre is connected to an analog pin of the arduino which behaves as an input and the output is translated as a change in voltage through the PWM pin of the Arduino.

#### Table for Pin connection

| Potentiometre | Arduino UNO |
| ------------- | ------------- |
| VCC | 5V |
| GND | GND |
| OUT | Analog Pin 0 |

| Probes | Arduino UNO |
| ------------- | ------------- |
| FaultPin | Digital Pin 3 |
| GND | GND |

#### Code
```
int FaultPin = 3;      // Digital pin 3 configured to introduce voltage change
int analogPin = A0;   // potentiometer connected to analog pin 9
int x = 0, y = 0; 
float z = 0;         // variable to store the read value

void setup() {
  pinMode(FaultPin, OUTPUT);// sets the pin as output
  pinMode(analogPin, INPUT);// sets the pin as input
  Serial.begin(9600);
}

void loop() {
  x = analogRead(analogPin);  // read the input pin
  y = map(x, 0, 1023, 0, 255);
  z = map(y, 0, 255, 0, 5);
  analogWrite(FaultPin, y);
  Serial.println(z);
}
```
#### Fault Injected at the power supply of the Motor

##### Case 1: Ideal Scenario
https://github.com/ppattanaik/VSD_SquadronMini_Internship/assets/63561037/555b81cb-e765-4465-922c-f93c6384141b

- This video represents the ideal behaiviour of the motor.

##### Case 2: Effect of Injected Fault
https://github.com/ppattanaik/VSD_SquadronMini_Internship/assets/63561037/2ff0722d-7454-407d-9177-daa418c8143b

- To Check out the live design [Click Here](https://www.tinkercad.com/things/bEV4ZL2BgTA-tremendous-blad/editel?sharecode=dONx5pNZ-kcBgRq8N-Fbj51CuZ2Xen0yxf2Qs6F0zsc)
- This video demonstrates the effect of the injected fault on the motor. As we can see, whenever there is a voltage change, the motor's RPM changes drastically. This will create abnormality in the functioning of the lid of the smart dustbin.

##### Case 3: Protection from Injected Fault
https://github.com/ppattanaik/VSD_SquadronMini_Internship/assets/63561037/d04fc50a-1f1d-4610-b299-23ec7672ef06

- To Check out the live design [Click Here](https://www.tinkercad.com/things/1wDC9IvfqZW-magnificent-stantia-juttuli/editel?sharecode=3IyZzXqIk9Y8Fv5M_xK6bjg_vvBQxdZCdlZ0B5tKNvM)

- This video represents the protection of the circuit by adding capacitors across the power supply. Here, whenever there is a voltage change, the capacitors compensate for the same helping to maintain a steady voltage due to which RPM fuctuations for the motor are balanced.

#### Fault Injected at the power supply of the Ultrasonic Sensor

##### Case 1: Ideal Scenario
https://github.com/ppattanaik/VSD_SquadronMini_Internship/assets/63561037/38e6eed3-7dcb-4d84-8a5d-59643a4ef531

- This video represents the ideal behaiviour of the ultrsonic sensor.

##### Case 2: Effect of Injected Fault
https://github.com/ppattanaik/VSD_SquadronMini_Internship/assets/63561037/cc78fcd5-5797-417d-b7df-c3748fe43e6e

- To Check the live design [Click Here](https://www.tinkercad.com/things/eBlMO06JBN8-brilliant-amberis/editel?sharecode=GxdkyT9tzj8WzQl3LzucDrWravYdJ5jXIrriUrjyafk)
- This video demonstrates the effect of the injected fault on the ultrasonic sensor. As we can see, whenever there is a voltage change, the ultrasonic sensor fails to read the position of the object placed in front of it. This will create abnormality in the object detection functionality of the smart dustbin.


##### Case 3: Protection from Injected Fault
https://github.com/ppattanaik/VSD_SquadronMini_Internship/assets/63561037/8745a390-da35-4075-97cb-2407ccb0b17e

- To Check the live design [Click Here](https://www.tinkercad.com/things/e8WJiBFlT4m-magnificent-jarv-krunk/editel?sharecode=7ypKf_hyKu80MO69_4_UApOJacJRzaI3YmZBkJSjlMQ)
- This video represents the protection of the circuit by adding capacitors across the power supply. Here, whenever there is a voltage change, the capacitors compensate for the same helping to maintain a steady voltage due to there is no abnormal variation in the reading of the ultrasonic sensor.

#### Fault Injected at the PD7 pin of the VSDSquadron Mini

##### Case 1: Ideal Scenario
https://github.com/ppattanaik/VSD_SquadronMini_Internship/assets/63561037/3dace670-d682-4a39-b12c-b5fa99738c5a

This video represents the ideal behaiviour of the Smart Dustbin.

##### Case 2: Effect of Injected Fault
https://github.com/ppattanaik/VSD_SquadronMini_Internship/assets/63561037/e7cb8020-8561-4da4-9a9d-e2f864e165e5

This video demonstrates the effect of the injected fault on the VSDSquadron Mini. As we can see, whenever there is a voltage change at the PD7 pin, the VSDSquadron Mini board resets as the PD7 is internally connected to the same. This will create abnormality in the overall working of the smart dustbin.

##### Case 3: Protection from Injected Fault
Ensure proper insulation of the PD7 pin to prevent exploitation.

# Falcon Flight

Welcome to the **Falcon Flight** GitHub repository! This project contains all the design files, and documentation for the Falcon Flight embedded control board.


## 🚀 Overview

Project Falcon Flight (previously The Blimp Project) is a project focused on engineering a complete blimp gondola under extreme design constraints. In years past, a mix of motor driver issues and reversed battery polarity led to only a fraction of SMCS students getting their blimps in the air before the summer. Hence, the Falcon Flight embedded controller board was developed to ease the progression of this project.

The Falcon Flight custom PCB was designed by a SMCS student, for SMCS students. This board has numerous improvements over the previous protoboard-based flight controllers, and just needs to be connected to a Raspberry Pi to get started. This board was designed with stability and longevity in mind, and includes the following features:

* **4x motor control** (bidirectional, PWM capable)
* **Dual LiPo battery design** (1 for motors, 1 for Pi)
* **USB-C charging**
* **Integrated 9-DoF sensor suite**
* **Stable 5v power for the Raspberry Pi**

For a more comprehensive list of features and documentation, visit the [hardware documentation](https://github.com/PHS-SMCS/falcon-flight/tree/main/docs), or the [blog post](eliferrara.com).


## 📦 What You'll Find in This Repo

* [`PCB/`](./PCB/) – EasyEDA source files & Gerbers
* [`images/`](./images/) – 3D renders & and silkscreen illustrations
* [`docs/`](./docs/) – Schematics (PDF), BOM, user guide


## 🐍 Python Library

Looking for interface code? Check out the dedicated Falcon Flight python library to get started.

**Library Repository**: [blimp-utils](https://github.com/Ballistyxx/blimp-utils)


## ✅ Do’s

* ✅ Charge the batteries via the built-in USB-C port, using any USB-C power source (chromebook chargers included!)
* ✅ Charge batteries while using the board
* ✅ Connect additional sensors via the 6-pin IO breakout
* ✅ Connect up to four motors to the motor driver `out` pins


## ❌ Don’ts

* ❌ **Don't unplug the internal batteries under ANY circumstances**
   
This greatly reduces the chances of destroying the internal circuitry via reversed polarity. Out of all the 'don'ts', this is the most important.
* ❌ Don’t power the Raspberry Pi via its Micro-USB port
    
Different voltages and a disconnected ground net will lead to instability and circuit damage if the Raspberry Pi is powered by both the battery & the Micro-USB port.
* ❌ Don't stall the motors when running
    
Stalling the motors can heat up the windings and drastically shorten the lifespan of the motors, as well as the motor driver ICs.
* ❌ Don't disconnect the 40-pin header without shutting off the board first
    
Improper shutdown can corrupt data on the Raspberry Pi, and can leave the sensor suite in an unrecoverable state.


## 📄 License

This project is licensed under the MIT License.


## 📬 Support / Questions

Open an issue or contact the designer directly at [eli.ferrara256@gmail.com](mailto:eli.ferrara256@gmail.com)


**Designed by Eli Ferrara, Class of 2026**

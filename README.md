# Automatic-Feeder-System-for-Betta-Fish
ESP32-based Environmental-Aware Feeding Controller

Overview

This project implements an automatic feeding system for Betta fish (Betta splendens) using an ESP32 microcontroller.
The system dispenses feed based on water temperature and pH conditions, aiming to maintain feeding consistency while avoiding overfeeding and environmental stress.

The design prioritizes fish safety, system reliability, and repeatable behavior under real-world hardware constraints such as sensor noise, actuator non-idealities, and power instability.

Biological Constraints (Design Input)

The system behavior is based on typical Betta care ranges:

Water temperature: 26–28 °C

pH range: 6.5–7.5

Feeding frequency: 1–2 times per day

Overfeeding risk: high (uneaten food degrades water quality)

These constraints directly influence control logic and safety limits.

System Architecture
Hardware Components

ESP32

Main controller

ADC for pH sensor

PWM generation for servo

DS18B20

Water temperature sensor (immersed, waterproof)

PH-4502C

Analog pH sensor module

High-noise signal, requires filtering

Micro Servo

Controls mechanical feed dispensing

Logical Block Diagram
[DS18B20] ─┐
           ├─> ESP32 ──> Servo (Feed Dispenser)
[PH-4502C] ┘

Control Strategy
Feeding Logic

The system uses rule-based conditional control with an explicit state machine.

Feeding is allowed only if:

Water temperature is within a safe range

pH is within acceptable bounds

Minimum time since last feeding has elapsed

This prevents feeding during stressful water conditions.

State Machine
IDLE
 └─> FEEDING
       └─> WAIT
             └─> IDLE


IDLE
Monitor sensors, no actuation.

FEEDING
Servo rotates to release a controlled amount of feed.

WAIT
Enforces cooldown (hours-scale) to avoid overfeeding.

Timing & Execution

Main loop rate: ~10 Hz

Sensor sampling:

DS18B20: periodic read (conversion delay considered)

pH sensor: multiple ADC samples per cycle

Actuation: non-blocking timing using millis()

Blocking delays are avoided in the control path.

Sensor Processing
Temperature (DS18B20)

Resolution configurable (9–12 bit)

Higher resolution increases conversion time

Used as a hard gate for feeding permission

pH Sensor (PH-4502C)

Analog sensor with significant noise

Sampled multiple times

Filtered using a moving average filter
(example: window size = 10)

Power & Hardware Considerations

Servo generates short current spikes

Brown-out risk on ESP32 if powered from same rail

Recommended setup:

Separate 5–6 V supply for servo

Common ground with ESP32

Servo only powered during FEEDING state

Safety & Fish Welfare Measures

Feeding blocked if:

Temperature < 25 °C or > 30 °C

pH outside configured safe range

Servo motion limited to predefined angles

Maximum feeding frequency enforced

System starts in IDLE state

Calibration
DS18B20

Factory calibrated

Placement ensures full water contact

PH-4502C

Manual calibration using buffer solutions:

pH 4.00

pH 6.86

pH 9.18

Calibration constants stored in firmware

Known Limitations

No direct measurement of feed mass

Open-loop servo positioning

No dissolved oxygen monitoring

No hardware watchdog

These limitations are documented to reflect real system constraints.

Possible Improvements

Add load cell to measure feed mass

Add RTC to enforce strict feeding schedule

Add water level sensor

Replace servo with stepper + mechanical gate

Add alert when pH/temperature out of range

Project Goals

This project demonstrates a practical embedded control system applied to aquatic animal care, focusing on:

Sensor uncertainty

Actuator safety

Deterministic firmware behavior

Real-world power and timing constraints

Author

Abdullah Khalid Fadillah
Computer Systems Engineering
Focus: Embedded Systems & Robotics

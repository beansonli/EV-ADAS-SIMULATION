# EV ADAS Dashboard

A little electric car that doesn't exist, having a very real existential crisis about whether something is too close to it.

Built on an STM32 microcontroller, simulated entirely in software, and watched over by a glowing Python dashboard.

## What is this thing?

Firmware running on a simulated STM32F103C8T6 (Blue Pill) that tracks speed, battery charge, motor temperature, drive mode, and three ultrasonic sensors (here, simulating an ADAS system) keeping watch on the front, left, and right. All data gets streamed out over UART to a Python dashboard for you to monitor over.


## What you can poke at and watch happen

- **Speed, SOC (battery %), torque, motor temperature, and range** — all update live and react to what you do to the inputs
- **Forward collision alarm** — bring the front sensor distance down and watch it escalate from calm, to WARNING, to full CRITICAL panic with a fault state
- **Blind-spot detection** — left and right sensors light up their own LEDs when something's lurking too close at speed
- **Drive modes** — ECO / NORMAL / SPORT, each scaling torque differently
- **Fault injection and recovery** — manually trigger an overheating motor, a dying battery, or a collision fault, then bring the system back from the dead with one command
- **Alarm priority levels** — NONE → ADVISORY → WARNING → CRITICAL, each with its own LED behavior


| Component | Role |
|---|---|
| **Potentiometers** | Stand in for the accelerator, brake, SOC, and motor temperature — slide them and the car reacts |
| **3 × Ultrasonic sensors** | Front, left, right — actively deciding if you're about to hit something or merge into a ghost |
| **LEDs** | Collision, left blind spot, right blind spot, and fault indicator — your at-a-glance panic lights |
| **Virtual UART Terminal** | PICSimLab's built-in terminal, where you can watch the system respond in real time |

## What you need installed

- **STM32CubeIDE** — to build the firmware
- **PICSimLab** — pretends to be the actual chip
- **VSPE (Virtual Serial Ports Emulator)** — creates a virtual serial pair so PICSimLab and Python can communicate to each other
- **Python 3**, plus:
  ```
  pip install pyserial matplotlib numpy
  ```

## How to actually run this 

**1. Set up your virtual serial pair in VSPE**
Create a paired pair of ports (say, COM1 ↔ COM4). One side goes to PICSimLab, the other goes to Python.

**2. Build and simulate the firmware**
Build the project in STM32CubeIDE, load it into PICSimLab, point PICSimLab's UART to one end of your virtual pair (e.g. COM4), and hit run.

**3. Launch the dashboard**
```
python ev_adas_dash.py --port COM1
```
(swap `COM1` for whichever end Python should be listening on)

**No simulator running and just want to see the dashboard exist?**
```
python ev_adas_dash.py --demo
```

## A note on the terminal
Heads up: `help` currently doesn't print anything useful in the virtual terminal, so don't rely on it; rather use tools such as CuteCom for the sake of the same.

## Background
Built as part of an automotive embedded systems internship project — equal parts firmware engineering (state machines, sensor polling, UART protocol) and an excuse to make matplotlib look like it belongs in a car.

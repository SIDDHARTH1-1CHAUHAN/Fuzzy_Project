````markdown
# fuzzy_traffic_controller
A fuzzy logic based traffic light controller using scikit-fuzzy and SUMO.
This project is designed to **reduce the waiting time of emergency vehicles** at intersections by prioritizing them using a fuzzy logic algorithm integrated with SUMO simulation.

## Team Members
1. Siddharth Chauhan (22105048)  
2. Saksham Gumber  
3. Tanish Jindal (22105092)  

## Requirements
- [Python 3](https://www.python.org/downloads/)  
- [SUMO](https://www.eclipse.org/sumo/)  
- [SUMO-GUI](https://sumo.dlr.de/docs/sumo-gui.html)  
- Python packages: `scikit-fuzzy`, `numpy`, `matplotlib`  

Install packages via pip:
```bash
pip install scikit-fuzzy numpy matplotlib
````

Or using Conda:

```bash
conda install -c conda-forge scikit-fuzzy numpy matplotlib
```

## Running the Program

Run the fuzzy logic controlled traffic simulation:

```bash
python fuzzy_controlled_simulation.py
```

Run the uncontrolled traffic simulation (used as a baseline for comparison):

```bash
python uncontrolled_simulation.py
```

## Overview

The project implements a **fuzzy logic-based adaptive traffic light control system** using Python and [SciKit-Fuzzy](https://scikit-fuzzy.github.io/scikit-fuzzy/overview.html).
Traffic simulation is performed with SUMO on a four-junction network. Vehicle routes are defined in proportions, and the [TraCI interface](https://sumo.dlr.de/docs/TraCI.html) is used to connect SUMO with Python for real-time control.

## Starting SUMO

Check if the environment variable `SUMO_HOME` is defined. `SUMO_HOME` should point to the SUMO installation directory.
After setting the environment variable, the SUMO GUI is launched, and the pre-built network is loaded. `traci` is imported to start the simulation.
Lane IDs affected by each traffic light and traffic light IDs at intersections are retrieved from `traci`.

## Pre-Simulation (Building the Road Networks)

The road network is built using SUMO GUI. Junctions and lane connections are defined, and XML configuration files are generated.
Eight routes are defined with a clear start and end position/state.

## The Simulation

SUMO runs with a default step of 1 second. The simulation runs approximately **16,000 steps**.
At each step, the following are collected:

* Number of moving vehicles
* Number of stopped vehicles at traffic lights
* Maximum waiting time among stopped vehicles
* Presence and count of emergency vehicles

There are **two main versions** of the simulation:

1. **Fixed-time Control:** Each route gets a 90-second green light, regardless of traffic. Serves as a control baseline.
2. **Fuzzy Logic Control:** Every 7 steps, traffic data (vehicle counts, waiting times, emergency vehicle presence) is sent to the fuzzy controller to decide whether to switch the traffic light.

## The Fuzzy Logic Controller

Implemented using `scikit-fuzzy`, the controller accepts **five inputs**:

1. Number of vehicles on red lanes
2. Number of vehicles on green lanes
3. Maximum waiting time on red lanes
4. Number of emergency vehicles on red lanes
5. Number of emergency vehicles on green lanes

**Membership Functions:**

* **Vehicles (0–12):** `too-small`, `small`, `much`, `too-much`
* **Waiting Time (0–50):** `negligible`, `okay`, `much`, `too-much`
* **Emergency Vehicles (0–2):** `absent`, `present`, `much`

**Traffic Light Output:**
A value between 0 and 1, where 0 → no switch needed, 1 → switch needed. Threshold is set at 0.5.

**Rule System:**
The input memberships are combined using fuzzy rules to compute the output, determining whether the traffic light should switch.

### Membership Graphs

* **Number of Vehicles**

  <img width="499" alt="image" src="https://user-images.githubusercontent.com/39713155/145992825-45fec009-db91-44b0-8ce2-f1ba2d19becc.png">

* **Maximum Vehicle Waiting Time**

  <img width="508" alt="image" src="https://user-images.githubusercontent.com/39713155/145992878-20eaef8b-4f15-4397-8634-a3df1502fbfb.png">

* **Number of Emergency Vehicles**

  <img width="398" alt="image" src="https://user-images.githubusercontent.com/39713155/145992918-7030c6bc-5bd6-45fd-bd08-5fa67ec5acd0.png">

* **Traffic Light Output**

  <img width="316" alt="image" src="https://user-images.githubusercontent.com/39713155/145992952-c43a5fd1-135d-47ec-a935-06774c547848.png">

The output is a decimal from 0 to 1. Values < 0.5 mean the traffic light remains unchanged; values ≥ 0.5 trigger a switch.

## Conclusion

Performance evaluation shows that the fuzzy logic controller improves intersection throughput and reduces waiting times:

* Average waiting time for regular vehicles decreased by ~25%
* Average waiting time for prioritized emergency vehicles decreased by ~77%
* Moving vehicles increased by ~6%
* Stopped vehicles decreased by ~7%

This demonstrates that **adaptive fuzzy traffic control** can efficiently manage traffic and prioritize emergency vehicles without major overhead.

## Notes on Errors & Fixes

1. `ModuleNotFoundError: skfuzzy` → Install via `pip install scikit-fuzzy`
2. `please declare environment variable 'SUMO_HOME'` → Set `SUMO_HOME` and update system PATH
3. `FileNotFoundError: [WinError 2]` → Ensure SUMO `bin` folder is in system PATH

## Future Work

* Implement multi-intersection coordinated fuzzy control
* Integrate real-time traffic sensor data
* Apply reinforcement learning to optimize fuzzy rules and membership functions

```
```

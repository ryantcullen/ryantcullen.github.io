---
layout: project
title: "CARELab: Robotics Lab Curriculum"
tags: [MATLAB, RTDE, UR3e, Robotics, Education]
---

[&larr; Back to CARELab Robotics Research]({{ '/projects/CARELab/' | relative_url }})

## Overview

Many undergraduate robotics programs struggle with a tradeoff: teach pendant programming is approachable but hides underlying control concepts, while ROS-based frameworks are powerful but overwhelm students with prerequisite Linux knowledge, command-line tooling, and configuration overhead before they ever touch a robot.

To address this, I developed a **Windows-native, MATLAB-centered control framework** for Universal Robots e-series manipulators using the **Real Time Data Exchange (RTDE)** protocol. MATLAB is already a core tool in most engineering curricula, so students can start interacting with real industrial hardware immediately — no Linux, no ROS installation, no middleware. The framework communicates directly with the UR3e over TCP/IP via a standard Ethernet connection.

This work was presented as a work-in-progress paper at the **2026 ASEE Annual Conference & Exposition**: *"MATLAB/Simulink-Based Open-Source Robot Control Framework for Undergraduate Robotics Curriculum."*

---

## Design Rationale

A key challenge was selecting the right combination of OS, control method, and simulation platform for an undergraduate lab setting. The flowcharts below summarize the decision process.

For **direct hardware control**, Linux supports both MATLAB and ROS2, but Windows — the dominant OS in university labs — only supports MATLAB via RTDE. ROS2 on Windows does not support direct UR hardware control, making MATLAB + RTDE the clear choice for a Windows-native workflow.

<p align="center"><img src="{{ '/assets/images/carelab/hardware_flowchart.png' | relative_url }}" alt="Hardware control platform selection flowchart" width="397"></p>

For **simulation**, Linux provides full access to URSim and Gazebo natively. On Windows, running a Linux VM gives the same URSim + URStudio access. Running natively on Windows limits you to URStudio only (a browser-based PolyscopeX emulator), which does not support external control methods like RTDE or ROS — only teach pendant-style interaction.

<p align="center"><img src="{{ '/assets/images/carelab/sim_flowchart.png' | relative_url }}" alt="Simulation platform selection flowchart" width="516"></p>

The final architecture uses **MATLAB + RTDE on Windows** for hardware control, with **URSim in a Linux VM** for simulation and **URStudio** for quick pose exploration.

---

## System Architecture

The control pipeline runs entirely on Windows 11 using MATLAB's Robotics System Toolbox and MathWorks' official UR Series support package. RTDE serves as the communication layer, enabling real-time state feedback and joint-level control commands over TCP/IP.

The framework also supports simulation-first workflows using **URSim** (a 1:1 physics simulator) and **URStudio** (a browser-based PolyscopeX emulator), allowing students to validate their code before deploying to the physical robot. URSim with MATLAB over RTDE is functionally identical to controlling the real hardware.

---

## Lab Modules

I authored four progressive lab modules that take students from zero to controlling a real industrial robot.

**Lab 1 — Installation & RTDE Setup:** Students configure the full MATLAB-to-UR3e communication pipeline, including RTDE networking, URCapX plugin installation, and static IP configuration. By the end, they have live joint state feedback and can send their first movement command.

**Lab 2 — Safety & Teach Pendant:** Hands-on introduction to the UR3e's safety system and Teach Pendant interface. Covers E-stops, virtual safety planes, force/speed limits, payload configuration for the Robotiq 2F-85 gripper, and basic TP programming with waypoints.

**Lab 3 — Forward Kinematics:** Students derive the UR3e's forward kinematics from first principles using screw theory (Product of Exponentials), implement the model in MATLAB, and validate their results against both URSim and the physical robot hardware.

**Lab 4 — Pick-and-Place (Capstone):** Students execute a practical pick-and-place task on the physical UR3e, first using the Teach Pendant and then replicating it programmatically through MATLAB + RTDE to compare the two workflows.

---

## Key Design Decisions

**MATLAB over ROS:** ROS requires Linux, significant prerequisite coding skills, and heavy configuration overhead. MATLAB runs natively on Windows, is already familiar to engineering undergraduates, and reduces the time from zero to robot interaction from days to minutes.

**RTDE over Teach Pendant programming:** The TP is essential for commissioning and safety, but it obscures the underlying mechanics. RTDE gives students direct joint-level access from a general-purpose programming environment, enabling them to implement their own controllers and explore lower-level behavior.

**Simulation-first workflow:** URSim provides a 1:1 physics simulation that behaves identically to the real hardware when controlled via RTDE. Students can validate code safely before touching the physical robot.

[&larr; Back to CARELab Robotics Research]({{ '/projects/CARELab/' | relative_url }})

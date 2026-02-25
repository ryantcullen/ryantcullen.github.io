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

<img src="{{ '/assets/images/carelab/hardware_flowchart.png' | relative_url }}" alt="Hardware control platform selection flowchart" width="690">

For **simulation**, Linux provides full access to URSim and Gazebo natively. On Windows, running a Linux VM gives the same URSim + URStudio access. Running natively on Windows limits you to URStudio only (a browser-based PolyscopeX emulator), which does not support external control methods like RTDE or ROS — only teach pendant-style interaction.

<img src="{{ '/assets/images/carelab/sim_flowchart.png' | relative_url }}" alt="Simulation platform selection flowchart" width="690">

The final architecture uses **MATLAB + RTDE on Windows** for hardware control, with **URSim in a Linux VM** for simulation and **URStudio** for quick pose exploration.

---

## System Architecture

The control pipeline runs entirely on Windows 11 using MATLAB's Robotics System Toolbox and MathWorks' official UR Series support package. RTDE serves as the communication layer, enabling real-time state feedback and joint-level control commands over TCP/IP.

The framework also supports simulation-first workflows using **URSim** (a 1:1 physics simulator) and **URStudio** (a browser-based PolyscopeX emulator), allowing students to validate their code before deploying to the physical robot. URSim with MATLAB over RTDE is functionally identical to controlling the real hardware.

---

## Lab Modules

The curriculum is structured as four progressive modules, each building on the last.

### Lab 1 — Installation and RTDE Setup

Students configure the full communication pipeline between a Windows PC and the UR3e running Polyscope X:

- Install the Robotics System Toolbox and UR Series support package in MATLAB
- Install the URCapX plugin and URServerscript on the Teach Pendant
- Configure static IP addresses and subnet masks on both the PC and Teach Pendant
- Enable RTDE on the robot and verify the connection with a ping test
- Run the MathWorks hardware setup wizard to confirm live joint state feedback
- Test basic RTDE commands: read joint angles, send a small movement command

The goal is a minimal-scope first lab that establishes a reliable connection so later modules don't fail due to setup issues.

### Lab 2 — Teach Pendant, Safety, and Basic Motion

Students get hands-on with the Teach Pendant interface and the UR3e's safety system:

- **Emergency Stop** — test the E-stop button; understand immediate motor power cutoff
- **Payload Settings** — configure the Robotiq 2F-85 gripper's mass, center of gravity, and TCP offset from the datasheet
- **Safety Planes** — create virtual walls that restrict the robot's working area (up to 8 planes with configurable position and orientation)
- **Robot Limits** — set speed, force, power, and momentum limits for the TCP, elbow, and individual joints
- **Protective Stops** — understand what triggers automatic stops (crossed safety planes, exceeded force/speed/position limits) and how to diagnose and clear them
- **Basic Movement** — power up the arm, use the Move screen to command translations and rotations in different reference frames
- **Program Creation** — build a simple two-waypoint program in the Program Tree and execute it

The module also includes a safety checklist that students complete before every subsequent session.

### Lab 3 — Forward Kinematics via Screw Theory

Students derive the forward kinematics of the UR3e from first principles using the **Product of Exponentials (PoE)** formulation:

**Pre-lab derivation:**
- Identify the unit rotation axis and a point on the axis for each of the 6 joints from a provided schematic diagram
- Compute the 6x1 twist vector for each joint using the cross-product relationship
- Assemble the 4x4 home pose matrix from the robot's link lengths

**MATLAB implementation:**
- Enter the derived screw axes and home matrix into the provided `UR3e_FK_Lab.m` file
- Run built-in self-tests that check against known analytical results (errors should be on the order of machine epsilon, ~10^-16 m)

**Simulation verification:**
- Determine the angle offset mapping between the screw-convention zero and the simulator's home angles
- Command arbitrary poses in URSim, read the TCP position, and compare against the FK computation
- Calculate percent error between the derived FK and the simulator's ground truth for 5+ poses

**Assessment questions** cover the UR3e's kinematic structure, the physical meaning of shared rotation axes, and sources of error between the mathematical model and physical hardware.

### Lab 4 — Pick-and-Place (Capstone)

Students execute a practical pick-and-place task on the physical UR3e hardware:

- Record a sequence of joint waypoints and gripper commands manually via the Teach Pendant
- Execute the sequence using built-in TP commands to pick up and place an object
- Replicate the same sequence programmatically using RTDE commands through MATLAB
- Compare the two workflows and understand the tradeoffs between TP programming and external control

---

## Key Design Decisions

**MATLAB over ROS:** ROS requires Linux, significant prerequisite coding skills, and heavy configuration overhead. MATLAB runs natively on Windows, is already familiar to engineering undergraduates, and reduces the time from zero to robot interaction from days to minutes.

**RTDE over Teach Pendant programming:** The TP is essential for commissioning and safety, but it obscures the underlying mechanics. RTDE gives students direct joint-level access from a general-purpose programming environment, enabling them to implement their own controllers and explore lower-level behavior.

**Simulation-first workflow:** URSim provides a 1:1 physics simulation that behaves identically to the real hardware when controlled via RTDE. Students can validate code safely before touching the physical robot.

[&larr; Back to CARELab Robotics Research]({{ '/projects/CARELab/' | relative_url }})

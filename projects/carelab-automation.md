---
layout: project
title: "CARELab: UR3e Automation & Control"
tags: [ROS2, UR3e, Robotics, Python, Automation]
---

[&larr; Back to CARELab Robotics Research]({{ '/projects/carelab/' | relative_url }})

## Overview

Alongside the lab curriculum development, I am building out the UR3e's full automation and control stack for research applications at the CARE Lab. The central challenge: integrating ROS2 into a workflow anchored on a Windows 11 machine running MATLAB — a combination that no single platform supports natively. After a thorough investigation of the constraints, I designed a three-node architecture that bridges the gap using a dedicated Linux single-board computer as a ROS2 middleman, preserving the existing MATLAB workflow while enabling full ROS2 capabilities.

---

## The Problem: ROS2 on Windows

The lab's existing setup — MATLAB on Windows 11 controlling the UR3e and Robotiq 2F-85 gripper via RTDE — works well for direct hardware control. But extending the system to ROS2 for research applications introduced a fundamental platform conflict.

**The UR ROS2 driver is Linux-only.** The `ur_robot_driver` package depends on `apt`, `colcon`, `rosdep`, and strongly recommends a `PREEMPT_RT`-patched kernel for strict cycle time requirements. There is no Windows build, and Universal Robots has no plans to provide one.

**MATLAB's own ROS2 + UR workflow requires a Linux host.** MathWorks' documentation is explicit: the robot must be connected to a host Linux machine, and ROS2 Humble with its dependencies must be installed on Ubuntu 22.04. MATLAB can run on a separate Windows PC, but a Linux machine must sit between MATLAB and the robot.

**ROS2 on Windows is experimental.** ROS2 Humble's official Windows binary only supports Windows 10. Newer distributions have experimental Windows 11 support, but with known bugs and missing packages. Even with a working ROS2 installation on Windows, the UR driver still wouldn't be available.

**WSL2 doesn't solve it.** The UR ROS2 driver needs direct, low-latency network access to the robot controller. WSL2's virtualized networking introduces latency and unreliable connectivity that conflicts with real-time control requirements, and users report build errors attempting to compile the UR driver in WSL2 on Windows 11.

---

## The Solution: Linux SBC as ROS2 Bridge

The architecture introduces a Linux single-board computer — such as a Raspberry Pi 5 or NVIDIA Jetson — as a dedicated ROS2 bridge between MATLAB and the UR3e. This is the architecture MathWorks themselves describe in their documentation, just implemented with a compact, dedicated device instead of a full second PC.

```
[Windows 11 PC] ---Ethernet--- [Linux SBC] ---Ethernet--- [UR3e Controller]
   MATLAB                     ROS2 + UR Driver            PolyscopeX
   (DDS Client)               (ROS2 Bridge)               (ExternalControl URCapX)
```

The SBC runs Ubuntu 22.04 with ROS2 Humble and the UR ROS2 driver. It has direct, wired Ethernet access to the UR3e — no virtualization layer — giving it the reliable, low-latency network connection the driver requires. MATLAB on Windows communicates with the SBC over the ROS2 DDS network, and the SBC translates those commands into RTDE traffic for the robot.

An RPi 5 (4GB) is sufficient for this role. The UR ROS2 driver is not computationally heavy — it primarily shuttles RTDE packets and manages `ros2_control` loops. A quad-core ARM Cortex-A72 or better at 1.5GHz+, 2–4GB of RAM, and a wired Ethernet port are the only real requirements. A Jetson would be overkill unless GPU-accelerated inference (computer vision, LLM planning) is needed on the edge device later.

---

## Communication Architecture

Two parallel communication channels connect MATLAB on Windows to the ROS2 network on the SBC:

**SSH (administrative channel).** MATLAB uses `ros2device` to connect to the SBC over SSH, providing the SBC's static IP, username, and password. This connection handles administrative tasks: transferring launch scripts, starting and stopping ROS2 nodes remotely, and process cleanup.

```matlab
device = ros2device('192.168.1.100', 'jetson_user', 'password');
device.ROS2Folder = '/opt/ros/humble';
device.ROS2Workspace = '~/ur_ws/install';
```

**ROS2 DDS (data channel).** Once the UR driver is running on the SBC, MATLAB communicates with it over DDS (Data Distribution Service) — ROS2's underlying transport layer. MATLAB's ROS Toolbox includes a built-in ROS2 node that participates in DDS multicast discovery automatically. Both machines need to be on the same subnet and share the same `ROS_DOMAIN_ID`; no ROS master is required.

From MATLAB, the robot appears as a standard `urROS2Node` object with access to `/joint_states` for reading robot state and `/scaled_joint_trajectory_controller/follow_joint_trajectory` for sending motion commands.

Between the SBC and the UR3e, the ROS2 driver communicates over RTDE and the UR real-time interface — the same protocols used in the direct MATLAB-to-robot setup, but now managed by the `ros2_control` framework. On the PolyscopeX side, the **ExternalControl URCapX** hands control authority from the teach pendant to the ROS2 driver. When the operator presses Play on the External Control program, the robot begins accepting commands from the SBC.

---

## Gripper Integration

The Robotiq 2F-85 gripper posed its own integration challenge. In the direct RTDE setup, we controlled the gripper by modifying the `urserver.script` file to call Robotiq URCapX functions directly. But the **Robotiq URCapX and ExternalControl URCapX conflict with each other** on PolyscopeX and cannot run simultaneously — making the existing gripper control method incompatible with the ROS2 architecture.

The solution uses the **tool-comm-forwarder URCapX**, a separate PolyscopeX plugin designed to coexist with ExternalControl. It forwards the UR3e's RS485 tool flange interface — where the Robotiq gripper is physically connected — over the network to the SBC on TCP port 54321. On the SBC, `socat` creates a virtual serial device (`/tmp/ttyUR`) from that TCP stream, and a ROS2 gripper driver node communicates with the gripper via Modbus RTU over this virtual port.

The resulting node graph on the SBC:

```
SBC (Ubuntu 22.04 + ROS2 Humble)
├── ur_robot_driver
│     ├── RTDE ↔ UR3e (arm control)
│     └── use_tool_communication:=true
│
├── Robotiq ROS2 Gripper Node
│     ├── Modbus RTU ↔ /tmp/ttyUR (RS485 via tool-comm-forwarder)
│     └── Exposes /gripper/command, /gripper/activate as ROS2 services
│
└── Both publish to the DDS network
      └── MATLAB sees arm + gripper as ROS2 topics/services
```

This eliminates the Robotiq URCapX entirely, replacing it with the tool-comm-forwarder + a dedicated ROS2 gripper node. Notably, the Modbus RTU path that failed on Windows due to suspected enterprise firewall or serial driver issues works reliably on the Linux SBC — we had previously verified this on a separate Linux PC.

---

## Latency Analysis

Adding the ROS2 middleman introduces measurable but practically insignificant latency for our use case.

**Direct RTDE (~2ms cycle time).** RTDE on the e-Series runs natively at 500Hz. The path is: MATLAB sends command &rarr; RTDE TCP socket &rarr; UR3e controller executes. One network hop, one protocol, minimal serialization overhead.

**ROS2 middleman (~10–50ms end-to-end).** The full stack introduces three layers of latency: DDS serialization and network transit from MATLAB to the SBC (~0.5–5ms per hop), the `ros2_control` loop on the SBC (controller manager read/write cycle, trajectory interpolation, speed scaling), and then RTDE from the SBC to the UR3e. Without a real-time kernel on the SBC, some scheduling jitter is expected.

**Practical impact: negligible for our applications.** Trajectory-level commands — `moveJ`, `moveL`, waypoint following — take seconds to execute. Whether the command arrives 2ms or 30ms after it's sent doesn't meaningfully change behavior; the robot interpolates on-board. The latency penalty would only matter for tight servo-rate control (500Hz `servoJ` loops for force control or visual servoing), which our research applications don't require. For pick-and-place, demonstration tasks, and trajectory execution, the difference is imperceptible.

---

## Network Configuration

The UR3e and the Linux SBC both require **static IP addresses**. The UR driver launch command hardcodes the robot IP (`robot_ip:=192.168.1.10`), and the ExternalControl URCapX on the teach pendant must be configured with the SBC's IP so the robot can initiate its "call home" connection when the operator presses Play.

The Windows MATLAB machine **does not need a static IP**. It only initiates outbound connections — SSH to the SBC and DDS multicast discovery — so DHCP is fine. If campus enterprise networking blocks UDP multicast, DDS can be configured for unicast discovery by setting `ROS_DISCOVERY_SERVER` or configuring the FastDDS peer list to point at the SBC directly.

---

## Startup Sequence

1. Power on the UR3e, boot PolyscopeX, load the External Control program
2. Boot the Linux SBC (ROS2 Humble and the UR driver are pre-installed)
3. Open MATLAB on Windows, create the `ros2device` connection to the SBC
4. Launch the UR driver on the SBC (via MATLAB's auto-generated launch script or manual SSH)
5. Press Play on the PolyscopeX teach pendant to start the External Control program
6. Create `urROS2Node` in MATLAB and begin sending commands

[&larr; Back to CARELab Robotics Research]({{ '/projects/carelab/' | relative_url }})

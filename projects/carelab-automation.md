---
layout: project
title: "CARELab: UR3e Automation & Control"
tags: [ROS2, UR3e, Robotics, Python, Automation, NVIDIA Jetson, YOLOv8, Computer Vision]
---

[&larr; Back to CARELab Robotics Research]({{ '/projects/carelab/' | relative_url }})

## Overview

I am building a vision-guided manipulation system for the Universal Robots UR3e at the CARE Lab, using an **NVIDIA Jetson Orin** as the edge AI and control hub. The Jetson runs Ubuntu 22.04 with ROS2 Humble, drives the UR3e arm and Robotiq 2F-85 gripper, and runs GPU-accelerated object detection to close the perception-to-action loop entirely on-device.

---

## System Architecture

The Jetson Orin is the central ROS2 node: it runs the UR ROS2 driver for arm control, a dedicated gripper node for the Robotiq 2F-85 via RS485, and a YOLOv8 perception node for real-time object detection. All three components publish to the same ROS2 DDS network, allowing perception to directly inform manipulation without additional middleware.

```
Jetson Orin (Ubuntu 22.04 + ROS2 Humble)
├── ur_robot_driver
│     └── RTDE <-> UR3e (arm control)
│
├── Robotiq ROS2 Gripper Node
│     ├── Modbus RTU <-> /tmp/ttyUR (RS485 via tool-comm-forwarder)
│     └── Exposes /gripper/command, /gripper/activate as ROS2 services
│
└── YOLOv8 Perception Node
      ├── CSI camera -> hardware ISP -> GPU inference
      └── Publishes detected object poses to ROS2 DDS
```

---

## Edge AI Perception: YOLOv8

The Jetson's Ampere GPU and dedicated deep learning accelerators run YOLOv8 as a GPU-accelerated ROS2 perception node. A CSI-connected camera feeds into the Jetson's hardware ISP, which handles debayering, auto-exposure, and format conversion before frames reach the GPU, keeping the CPU out of the ingestion pipeline entirely.

Detection results publish directly to the ROS2 DDS network as standard message types, where the arm controller subscribes to object poses for motion planning. This closes the perception-to-action loop: the Jetson sees an object, publishes its pose, and the UR3e moves to it.

NVIDIA's TensorRT optimizer fuses layers, quantizes weights to FP16 or INT8, and auto-tunes CUDA kernels for the specific Orin hardware, pushing YOLOv8n from roughly 15-25 FPS in native PyTorch to 60+ FPS. That headroom keeps perception from being the bottleneck in the manipulation loop.

---

## Gripper Integration

Integrating the Robotiq 2F-85 with the ROS2 architecture required solving a URCapX conflict: the Robotiq URCapX and ExternalControl URCapX cannot run simultaneously on PolyscopeX, making direct gripper control incompatible with ROS2.

The solution uses the **tool-comm-forwarder URCapX**, which forwards the UR3e's RS485 tool flange interface over the network to the Jetson on TCP port 54321. On the Jetson, `socat` creates a virtual serial device (`/tmp/ttyUR`) from that TCP stream, and a ROS2 gripper driver node communicates with the gripper via Modbus RTU over this virtual port. This eliminates the Robotiq URCapX entirely and exposes the gripper as standard ROS2 services alongside the arm.

[&larr; Back to CARELab Robotics Research]({{ '/projects/carelab/' | relative_url }})

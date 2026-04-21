---
title: CARELab Robotics Research
summary: Developing a ROS2 perception and control pipeline for a UR3e collaborative manipulator with an NVIDIA Jetson Orin for edge AI, alongside a MATLAB-based lab curriculum, at SFSU's CARE Lab.
tags: [ROS2, MATLAB, UR3e, Robotics, Python, NVIDIA Jetson, YOLOv8, Computer Vision]
weight: 2
featured: true
link: https://www.careroboticslab.com
linkName: CARELab
---

As a graduate researcher at the **CARE Lab** (Control for Assistive and Rehabilitation Robotics Lab) at San Francisco State University, I am developing an edge AI perception and control system centered on an **NVIDIA Jetson Orin** and a Universal Robots UR3e 6-DoF collaborative manipulator. The Jetson serves as the system's GPU-accelerated ROS2 perception node — deploying YOLOv8-based object detection for vision-guided manipulation — and as the ROS2 bridge between a Windows/MATLAB control host and the robot's Robotiq 2F-85 gripper.

I also authored a MATLAB-based UR3e lab curriculum for undergraduate robotics, accepted for publication at the **2026 ASEE Annual Conference & Exposition**.

---

### UR3e Automation & Control

Developing a ROS2 perception and control pipeline for the UR3e with a Robotiq 2F-85 gripper, targeting vision-guided manipulation tasks. After a thorough investigation of platform constraints (the UR ROS2 driver is Linux-only and MATLAB's ROS2 workflow requires a Linux host), I designed a three-node architecture with an **NVIDIA Jetson Orin** serving as the ROS2 bridge between MATLAB on Windows and the robot, with integrated Robotiq gripper control via RS485 forwarding. The Jetson's Ampere GPU and dedicated inference hardware make it the edge AI layer of the system — currently being used to deploy **YOLOv8-based object detection** as a GPU-accelerated ROS2 perception node for real-time vision-guided manipulation.

[Read more &rarr;]({{ '/projects/carelab-automation/' | relative_url }})

---

### Robotics Lab Curriculum Development

Designed and authored a four-module laboratory curriculum that uses MATLAB and the Real Time Data Exchange (RTDE) protocol to give undergraduate students direct, hands-on control of an industrial UR3e robot without requiring Linux or ROS. The curriculum progressively builds from installation and safety through teach pendant operation, forward kinematics derivation via screw theory, and a capstone pick-and-place exercise.

[Read more &rarr;]({{ '/projects/carelab-labs/' | relative_url }})

---
title: CARELab Robotics Research
summary: Developing a MATLAB-based robotics lab curriculum and UR3e automation framework at SFSU's Control for Assistive and Rehabilitation Robotics Lab.
tags: [ROS2, MATLAB, UR3e, Robotics, Python]
weight: 2
featured: true
link: https://www.careroboticslab.com
linkName: CARELab
---

As a graduate researcher at the **CARE Lab** (Control for Assistive and Rehabilitation Robotics Lab) at San Francisco State University, I lead two parallel efforts centered on a Universal Robots UR3e collaborative robot arm: developing a hands-on lab curriculum for undergraduate robotics education, and building out the robot's automation and control capabilities for research applications.

This work was presented as a work-in-progress paper at the **2026 ASEE Annual Conference & Exposition**, titled *"MATLAB/Simulink-Based Open-Source Robot Control Framework for Undergraduate Robotics Curriculum."*

---

### Robotics Lab Curriculum Development

Designed and authored a four-module laboratory curriculum that uses MATLAB and the Real Time Data Exchange (RTDE) protocol to give undergraduate students direct, hands-on control of an industrial UR3e robot without requiring Linux or ROS. The curriculum progressively builds from installation and safety through teach pendant operation, forward kinematics derivation via screw theory, and a capstone pick-and-place exercise.

[Read more &rarr;]({{ '/projects/carelab-labs/' | relative_url }})

---

### UR3e Automation & Control

Designing the UR3e's ROS2 control architecture for research applications. After a thorough investigation of platform constraints (the UR ROS2 driver is Linux-only and MATLAB's ROS2 workflow requires a Linux host), I designed a three-node architecture using a dedicated Linux SBC as a ROS2 bridge between MATLAB on Windows and the robot, with integrated Robotiq gripper control via RS485 forwarding.

[Read more &rarr;]({{ '/projects/carelab-automation/' | relative_url }})

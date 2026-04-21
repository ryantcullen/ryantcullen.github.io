---
title: Reinforcement Learning Identification
summary: My senior undergraduate thesis project in which I developed a protocol to distinguish between learning and non-learning agents among a given population.
tags: [Reinforcement Learning, AI, Data Science, Research]
weight: 4
featured: false
link: https://github.com/ryantcullen/identifying-learners
linkName: Paper
---

My senior undergraduate thesis in computer science, exploring whether it’s possible to distinguish individual reinforcement learners from agents whose behavior was shaped by population-level evolution.

The core problem: a well-optimized non-learning agent and a genuine learner can look identical from the outside — both maximize reward. The paper develops a protocol to separate the two, testing Policy Iteration and Q-learning on Markov Decision Processes alongside detection methods including outlier detection and signal detection theory.

Outlier detection was sub-optimal overall but performed well under specific environment configurations. Signal detection methods showed more promise as a general approach. The paper also touches on a deeper implication: for individual learning to exist at all, the capacity to learn must itself have first been selected for at the population level.

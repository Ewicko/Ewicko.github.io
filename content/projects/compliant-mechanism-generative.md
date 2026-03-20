---
title: "Compliant Mechanism Generative Design"
description: "Topology-optimized compliant mechanisms with input and output ports."
type: "projects"
project_type: "Research Tool"
thumbnail: "/images/compliant-mechanism-topop/mechanism-business-card.png"
tags: ["Python", "FEniCSx", "MMA", "NumPy", "Matplotlib", "Paraview"]
duration: "Ongoing"
status: "Active"
weight: 1
math: false
---

## Overview

I created custom and robust topology optimizers for designing small strain flexures and compliant mechanisms. This tool leverages linear and nonlinear finite element analysis to generate optimal monlithic designs that can achieve complex mechanical behaviors through elastic deformation rather than traditional joints and linkages.

Built with future users in mind, this project connects to Transcend Mechanics' webapp through an API connection. Details below.

{{< video src="/images/compliant-mechanism-topop/mechanism_video.mp4" >}}

*Force inverter with 3:1 mechanical advantage*

## Solver Inputs & Outputs

### Flexures

**Inputs**

- Design domain dimensions
- Target stiffness in free direction(s)
- Required displacement range
- Material selection
- Material stress limits
- Obstacle locations (optional)
- Volume/mass limit (optional)

**Outputs**

- Optimized flexure topology (STL)
- Performance metrics

### Compliant Mechanisms

**Inputs**

- Design domain dimensions
- Target mechanical or geometric advantage
- Input port location
- Output port location
- Fixed/preserved regions
- Material selection
- Material stress limits
- Obstacle locations (optional)
- Volume/mass limit (optional)

**Outputs**

- Optimized mechanism topology (STL)
- Actual mechanical advantage
- Input/output relationships

## Technical Implementation

**Tech Stack:** Python, FEniCSx, MMA, NumPy, Matplotlib, Paraview

The optimizer uses a custom implementation of the Method of Moving Asymptotes (MMA) algorithm combined with FEniCSx based FEA solvers.

- **FEA:** Modified FEniCSx based FEA solvers made to work with MMA sensitivites/gradients.
- **Optimizer:** Custom implementation of the Method of Moving Asymptotes (MMA), adapted from Svanberg (1987).
- **Formulations:** Custom strain energy and virtual work formulations inspired by Koppen (2022).
- **Density-Based TO:** SIMP (Solid Isotropic Material with Penalization) method.

## Obstacle Avoidance

I also developed novel obstacle functionality, enabling definition of keep-out zones within the design domain. This allows optimization around existing components or mechanical constraints.

![Initial design with obstacle zone](/images/compliant-mechanism-topop/obstacle_start.png)

*Early iteration shows obstacle "keep out" region as white rectangle in the design domain*

![Optimized design avoiding obstacle](/images/compliant-mechanism-topop/obstacle_finish.png)

*Obstacle region respected in generated design (stiff in tx on top edge, compliant in ty)*

![Design without obstacle constraint](/images/compliant-mechanism-topop/without_obstacle.png)

*Has comparable performance to non-obstacle design shown (stiff in tx on top edge, compliant in ty)*

## Example Designs

These examples show some mechanisms generated. Currently, I'm using PLA as the working matieral and printing them out on my Creality K1C to test them.

![Physical prototypes of optimized flexures](/images/compliant-mechanism-topop/Flexures-on-table.png)

![Compliant inverter mechanism with input and output ports](/images/compliant-mechanism-topop/inverter-mechanism.png)

*Topology-optimized compliant inverter mechanism showing input and output ports*

## Access the Platform

I'm passionate about translational research and democratizing access to advanced engineering tools. These topology optimization solvers are deployed through Transcend's cloud-based platform at [beta.deflex.ai](https://beta.deflex.ai).

[Access beta.deflex.ai](https://beta.deflex.ai)

![beta.deflex.ai platform interface showing topology optimization in action](/images/compliant-mechanism-topop/dsolve.png)

## Future Development

Current work focuses on incorporating machine learning to predict optimal initial designs, reducing computation time. Additionally, multi-input/output and 3D capabilities are being developed to expand the design space for more complex applications.

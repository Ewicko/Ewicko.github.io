---
title: "Lorem Ipsum: A Sample Post"
date: 2026-03-18
description: "A sample blog post demonstrating all the features of this site — code blocks, math, images, and more."
draft: false
math: true
---

This is a sample blog post to demonstrate the formatting and features available on this blog. It covers everything from basic prose to code blocks, mathematical notation, and embedded media.

## Writing and Prose

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.

> "The purpose of computation is insight, not numbers." — Richard Hamming

Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

## Code Blocks

Here's an example of a Python function with syntax highlighting:

```python
import numpy as np

def topology_optimize(domain, volume_fraction=0.5, penalty=3.0):
    """Run SIMP-based topology optimization on a 2D domain."""
    density = np.ones(domain.shape) * volume_fraction
    for iteration in range(200):
        ke = compute_element_stiffness(density, penalty)
        u = solve_fem(ke, domain.loads, domain.bcs)
        sensitivity = compute_sensitivity(u, ke, density, penalty)
        density = optimality_criteria_update(density, sensitivity, volume_fraction)
    return density
```

And some inline code: the `density` array stores per-element material distribution values between 0 and 1.

## Mathematics

The SIMP (Solid Isotropic Material with Penalization) method relates element stiffness to density:

$$E_e = E_0 \cdot \rho_e^p$$

where $E_e$ is the element Young's modulus, $E_0$ is the base material modulus, $\rho_e$ is the element density, and $p$ is the penalization factor (typically $p = 3$).

The optimization problem is formulated as:

$$\min_{\rho} \quad c(\rho) = \mathbf{U}^T \mathbf{K} \mathbf{U} = \sum_{e=1}^{N} (\rho_e)^p \mathbf{u}_e^T \mathbf{k}_0 \mathbf{u}_e$$

subject to:

$$\frac{V(\rho)}{V_0} = f, \quad 0 < \rho_{\min} \leq \rho_e \leq 1$$

## Images

Here's an example of an inline image (using an existing project image):

![Compliant mechanism design](/images/compliant-mechanism-topop/mechanism-business-card.png)

## Lists

Things I find interesting:

- Topology optimization and generative design
- Reinforcement learning for engineering applications
- Nonlinear finite element analysis
- Diffusion models for structural design

## Links

Some useful references:

1. [Bendsøe & Sigmund (2003)](https://link.springer.com/book/10.1007/978-3-662-05086-6) — the foundational text on topology optimization
2. [TopOpt group at DTU](https://www.topopt.mek.dtu.dk/) — excellent educational resources
3. [FEniCSx documentation](https://docs.fenicsproject.org/) — the FEA framework used in my solvers

---

*This is a sample post. Delete it when you publish your first real article.*

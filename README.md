<p align="center"><img width=100% src="media/cudaSphSolverTitle.png"></p>

<h4 align="center">CUDA-based hardware-accelerated particle solver</p>

[![License: Unlicense](https://img.shields.io/badge/license-Unlicense-blue.svg)](http://unlicense.org/)

---
***
## Purpose

I created this project to explore single instruction, multiple data(SIMD) design for GPUs and further understand hardware-accelerated parallel computing via the CUDA Runtime API. This project is a portion of my undergraduate research at my university, where I explored using smoothed-particle hydrodynamics(SPH) as a method for generating flowmaps/flow textures.

While I am unable to show the code for my full research, I am able to show my simplified CUDA-based SPH implementation. This implementation follows Matthias Muller's methodology as dictated in his SIGGRAPH paper titled "Particle-Based Fluid Simulation for Interactive Applications"(sca03).

<p align="center"><img width=100% src="media/cudaSphSolverGif.gif"></p>

***
## Smoothed-particle Hydrodynamics Overview

Smoothed-particle Hydrodynamics(SPH) simulations are developed following a mesh-free Lagrangian approach to simulating particles. To keep things brief, this primarily requires approximating three equations to describe the density field, pressure forces, and viscosity forces. 

Since navier-stokes equations were built for the real world and not for this approximation of it, we can begin to start having issues quite quickly. For instance, how do we maintain a smooth falloff between particles exerting force on eachother, shouldn't water particles have some amount of attraction between eachother, and how does two particles overlaping effect the simulation? Well, thankfully this has already been studied to *great* depth. These solutions are included in our calculations and are called *smoothing kernels*. These kernels are what keep our simulation stable and accurate. Smoothing kernels are quite fascinating and I highly suggest you read more into them if you wish to expand this code. 

This SPH implementation does **not** account for phenomena such as air-water entrapment, surface tension, and particle-mesh interactions. Many computational fluid dynamics(CFD) simulations hold these to great importance, but my research did not require these to be accounted for in the simulation.

For brevity, and the fact that others have explained this far better, that is where my overview ends. I highly suggest exploring the original paper written on smoothed-particle hydrodynamics if you would like to explore this fluid simulation method in more depth!

#### 

***
## Q&A

### *Q*: Why can't I compile this code?

**A**: It's likely you do not have the [CUDA Runtime](https://developer.nvidia.com/cuda-downloads) installed. The NVCC will first compile all device(GPU) code and host calls to the GPU, then compilation and further linking will be done on your default/desired c++ compiler.

### *Q*: Why aren't you using any optimizations?

**A**: There are a vast amount of optimizations that can be made to improve SPH solvers, hence why they are such a hot topic in research. The most common implementation is nearest-neighbor searches for reducing the number of calculations to a local space. However, implementing something like quad-trees in an efficient manner was a bit tertiary to what I was doing with my research. If you are looking to improve this code that is where I would start though.

### *Q*: Would it be hard to add boundaries?

**A**: Not at all! Infact, you could pretty much do it by having static particles that pretty much just skip the ```calculateForces(...)``` and ```integration()``` step.
``` c++
        ...
		pArray[i].acceleration.x = 0.f;
		pArray[i].acceleration.y = 0.f;

		if (pArray[i].pType != STATIC && pArray[i].pType != OUTLET)
		{
            ...
```
If I get the time to return to this project, I would like to finish my implementation and also add constraints to create things like rigid-bodies.
---
title: "Practical Assignments: Introduction to Continuous Modeling"
date: 2022-12-04
draft: false
author: ["Noé Hsueh", "Juan Wisznia"]
---



## Practical Assignment 1: Simulation of Gas Dynamics
This practical assignment focuses on the study of gas dynamics in a system of 
particles in motion within a box. The system models particles in a box in 
the plane that repel each other and collide with the walls.

A repulsive potential is employed, which varies based on the distance between 
the particles and an interaction constant. The calculation of forces between 
particles is described, and the manner in which they act in accordance with
the proposed potential is established.

[Click here to open the notebook (spanish only)](/tp_imc/tp1.jl.html)

{{< rawhtml >}}
<iframe src="/tp_imc/tp1.jl.html" width="100%" height="300"></iframe>
{{< /rawhtml >}}


## Practical Assignment 2: Image Compression (jpeg)

In this practical assignment, we will implement a simplified version of the .jpg 
file compression algorithm, based on the observation that real signal transforms 
are typically dominated by low frequencies with a limited contribution from high frequencies.

The strategy involves performing a signal transform, discarding frequencies above 
a threshold, and storing only a reduced set of frequencies, achieving compression. 
Signal recovery entails completing the transformed vector with zeros and applying 
the inverse transform. While the jpg algorithm adheres to this principle, it also 
employs the cosine transform instead of the Fourier transform due to its ability 
to work with real numbers and its greater concentration of information in low frequencies.

The cosine transform is implemented using the dct command in the FFTW library.

[Click here to open the notebook (only in spanish).](/tp_imc/tp2jl.html)

{{< rawhtml >}}
<iframe src="/tp_imc/tp2jl.html" width="100%" height="300"></iframe>
{{< /rawhtml >}}


## Practical Assignment 3: Diffusion Simulation in a Cup

This assignment revolves around solving an equation that models the diffusion of 
a substance in a liquid within a circular domain. The method of lines is employed, 
and polar coordinates are used for discretization and problem-solving. Neumann 
boundary conditions are imposed on the domain edges, taking into account the 
circular geometry's peculiarities. The aim is to numerically find the solution of the equation within a rectangle in the polar domain.

[Click here to open the notebook (only in spanish).](/tp_imc/tp3.jl.html)

{{< rawhtml >}}
<iframe src="/tp_imc/tp3.jl.html" width="100%" height="300"></iframe>
{{< /rawhtml >}}
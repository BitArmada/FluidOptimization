# FluidOptimization
Fluid Topology Optimization using Convolutional Neural Networks and Lattice Boltzmann simulation  

## Method Overview
The topology optimization algorithm is composed of three components
- Ground truth simulation (Lattice Boltzman Method) - code not included
- AI fluid force approximation model
- Gradient based optimization algorithm

## Fluid Force Approximation with a Convolutional Nueral Network (CNN)
Random strutures were generated on a cell grid 128x64 and then simulated numerically using LBM method for the equivalent of 10 seconds in relatively low reynolds number fluid flows. Data containing average fluid force values in the x and y directions was recorded for each fluid cell along with total change in force to provide a measure of turbulence (delta). The delta values were calculated using the following equation

$`θ = \sum_{t=1}^n \sqrt{(U_t - U_{t-1})^2 + (V_t - V_{t-1})^2}`$

This represents the sum of the magnitude of velocity vector changes in between simulation steps, where
- $`U`$ and $`V`$ are fluid force components X and Y respectively
- $`t`$ some simulation step index in total steps $`n`$

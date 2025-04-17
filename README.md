# FluidOptimization
Fluid Topology Optimization using Convolutional Neural Networks and Lattice Boltzmann Simulation  

## Method Overview
The topology optimization algorithm is composed of three components
- Ground truth simulation (Lattice Boltzmann Method) - code not included
- AI fluid force approximation model
- Gradient-based optimization algorithm

## Fluid Force Approximation with a Convolutional Neural Network (CNN)
Random structures were generated on a cell grid 128x64 and then simulated numerically using the LBM method for the equivalent of 10 seconds in relatively low Reynolds number fluid flows. Data containing average fluid force values in the x and y directions was recorded for each fluid cell along with a total change in force to provide a measure of turbulence ($`Τ`$). The turbulence values were calculated using the following equation

$`Τ = \sum_{t=1}^n \sqrt{(U_t - U_{t-1})^2 + (V_t - V_{t-1})^2}`$

Where the force turbulence metric $`Τ`$ is the sum of the magnitude of velocity vector changes between simulation steps
- $`U`$ and $`V`$ are fluid force components X and Y, respectively
- $`t`$ some simulation step index in total steps $`n`$

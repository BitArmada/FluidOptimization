# FluidOptimization
Fluid Topology Optimization using Convolutional Neural Networks and Lattice Boltzmann Simulation  

## Method Overview
The topology optimization algorithm is composed of three components
- Ground truth simulation (Lattice Boltzmann Method) - code not included
- AI fluid force approximation model
- Gradient-based optimization algorithm

## Fluid Force Simulation using Lattice Boltzmann Method (LBM)
Random structures were generated on a cell grid 128x64 and then simulated numerically using the LBM method for the equivalent of 10 seconds in relatively low Reynolds number fluid flows. Data containing average fluid force values in the x and y directions was recorded for each fluid cell along with a total change in force to provide a measure of turbulence ($`\tau`$). 

the force metrics $`F_x`$ and $`F_y`$ are simply averages of lift and drag forces over all simulation steps.

$$F_x = \frac{1}{n} \sum_{t=1}^n U_t$$
$$F_y = \frac{1}{n} \sum_{t=1}^n V_t$$

<!-- $$
\begin{aligned}
a^2 + b^2 &= c^2 \\
x &= y + z
\end{aligned}
$$ -->

The turbulence values represented by ($`\tau`$) were calculated using the following equation

$$F_\tau = \frac{1}{n} \sum_{t=1}^n \sqrt{(U_t - U_{t-1})^2 + (V_t - V_{t-1})^2}$$

Where the force turbulence metric $`F_\tau`$ is the sum of the magnitude of velocity vector changes between simulation steps
- $`U`$ and $`V`$ are fluid force components $`F_x`$ and $`F_y`$, respectively
- $`t`$ some simulation step index in total steps $`n`$


![Simulation Results](./images/simulationResults.png "Simulation Results")

These images display the locations of fluid forces on the boundries of randomly generated solids with the RGB value of each cell reflecting the X,Y and turbulence force values.

## Fluid Force Approximation with a Convolutional Neural Network (CNN)



## Gradient-based optimization algorithm
<!-- ![Alt text](./images/partialDrag.png "a title")
![Alt text](./images/partialLift.png "a title")
! -->

The optimization algorithm involves taking topological derivatives and using gradient descent to minimize a loss function.

$$
L\left( F_x,F_y,F_\tau\right)
$$

A loss function can be defined in terms of the outputs of the simulation $`F_x,F_y,`$ and $`F_\tau`$ and thus its derivative can expressed using the chain rule.

$$\frac{\partial L}{\partial S} = \frac{\partial L}{\partial F_x}\frac{\partial F_x}{\partial S} + \frac{\partial L}{\partial F_y}\frac{\partial F_y}{\partial S} + \frac{\partial L}{\partial F_\tau}\frac{\partial F_\tau}{\partial S}$$

where $`S`$ is the input state of the cell grid.

These gradients can be approximated by simulating small changes to a shape and evaluating their effects on drag, lift, etc. For example the gradient of drag can be found by simulating a small change $`h`$.

$$
\frac{\Delta F_x}{\Delta S} = \frac{F_x(S+h) - F_x(S)}{h}
$$

Here are a few examples of gradients approximated by changing one cell on a fluid-solid boundry and evaluating using an LBM simulation.

<!-- $$\frac{\partial F_x}{\partial S}$$ -->

Gradients of drag ($`F_x`$) on circle boundry

<img src="./images/partialDrag.png" alt="drawing" width="300"/>

Gradients of lift ($`F_y`$) on circle boundry

<img src="./images/partialLift.png" alt="drawing" width="300"/>

Gradients of turbulence ($`F_\tau`$) on circle boundry

<img src="./images/partialDelta.png" alt="drawing" width="300"/>

### Neural Gradients
The proccess of determining gradients with respect to an input shape using a numerical simulation is relatively costly and can be made much more efficient by substituting for a differentiable Neural Network approximation. This greatly decreases the time it takes to calculate fluid forces and allows the algorithm to compute gradients simbolically using backpropogation.

In this example the netowrk has predicted the fluid forces for a particular shape. 
<img src="./images/predictedForces.png" alt="drag maximization" />

After forces have been aproximated they can be used as inputs for a loss function, in this case torque maximization. Once loss has been calculated backpropogation can be applied to automatically find the gradients of the loss function with respect to the value of each cell.
<img src="./images/gradientmagnitude.png" alt="drag maximization" />

### Optimization Algorithm and Gaussian-Imagination Filter

Gradients alone are a helpfull tool to understand the impact of single cell changes on loss, however they fail to capture the affects of multi-cell changes. Additionally because they are continuous they are illsuited to fluid topology optimization that requires descrete solid fluid boundries.

To address these issues a filter is applied that creates simi-descrete boundries while allowing some cells to remain indeterminate. This called the "imagination" filter because unlike a level set function the indeterminant cells persist across iterations allowing for larger scale multi-cell changes. This function also contains a sharp jump that forces values closer to 0 or 1 and stimulates the convolutional network which is responsive to sharp edges.

The imagination function is defined as 
$$
Imagination\left(S\right)=\frac{\tanh\left(\left(S-t\right)\cdot w\right)+1}{2}\cdot\left(1-I\right)+x\cdot I
$$
Where $`S`$ is some input shape
- $`t`$ is the solid-fluid boundry threshold
- $`I`$ is the imagination strength that determines how long imagined shapes persist
- $`w`$ is the sharpness coefficient for the solid-fluid boundry

<img src="./images/ImaginationFunction.png" alt="drag maximization" />

Here is an example of the Imagination function (red) compared to the traditional level set function (blue). With each additional pass through the imagination filter, indeterminant values will decay and a shape will aproach the discrete level set function.

In practice in order to prevent getting stuck in local minima and avoid noise from the Neural Network, the Imagination filter is combined with a gaussian density filter.

Every iteration the network predicts the fluid forces on the current shape

<img src="./images/dragmax.gif" alt="drag maximization" />
<img src="./images/2x2.gif" alt="drag maximization" />




<img src="./images/torque.gif" alt="drag maximization" />

# MPM Algorithm

## Main Algorithm

The Material Point Method (MPM) algorithm comprises of 3 major parts.

1. Initialization

2. Solution phase for time step $t$ to $t + \delta t$
    A. Mapping from particles to nodes
        a. Compute nodal mass 
              $$ ((mv)_I)^t = \Sigma_p N_I(x_p)^t M_p $$

    B. Update the Momenta at nodes
              $$((mv)_I)^(t+\delta t) = ((mv)_I)^t + f_I \delta t$$
   
    C. Mapping from nodes to particles
        a. Update particle velocities
            i. Normal Implementation
         ii. Newmark Integration

      b. Update particle positions
         i. Normal Implementation
         ii. Newmark Integration

3. Reset the grid (if it was updated) and advance to the next time step

> **Note** MPM stores information at the particles and not at the nodes as it is in Finite Element Method.

## Nomenclature



# Reference

Nguyen, V. P. (2014). "Material point method: basics and applications", Cardiff University.

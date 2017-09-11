# MPM Algorithm

## Main Algorithm

The Material Point Method (MPM) algorithm comprises of 3 major parts.

1. Initialization

2. Solution phase for time step $t$ to $t + \delta t$
   A. Mapping from particles to nodes
      a. Compute nodal mass 
              $$ ((mv)_I)^t = \Sigma_p N_I(x_p)^t M_p $$
      b. Compute nodal momentum
              $$ ((mv)_I)^t = \Sigma_p N_I(x_p)^t ((Mv)_p)^t $$
      c. Compute nodal velocities
              $$ (v_I)^t = ((mv)_I)^t /over (m_I)^t   $$
      d. Compute gradient velocity
              $$   $$
      e. Compute gradient deformation tensor
              $$   $$
      f. Update volume
              $$   $$
      g. Update stress
              $$ (\sigma_p)^t = (\sigma_p)^t + \delta (\sigma_p)^t   $$
      h. Compute external force
              $$ (f_I)^(ext,t)  $$
      i. Compute internal force
              $$ (f_I)^(int,t) = -\Sigma V_p \sigma_p \grad N_I (x_p) $$
      h. Compute nodal force
              $$ f_I = (f_I)^(ext) + (f_I)^(int)  $$

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

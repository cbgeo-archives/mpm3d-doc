# MPM Algorithm

## Main Algorithm

The Material Point Method (MPM) algorithm comprises of 3 major parts.

1. Initialization

1. Solution phase for time step $t$ to $t + \delta t$
   I. Mapping from particles to nodes
      a. Compute nodal mass 
              $$ ((mv)_I)^t = \Sigma_p N_I(x_p)^t M_p $$
      a. Compute nodal momentum
              $$ ((mv)_I)^t = \Sigma_p N_I(x_p)^t ((Mv)_p)^t $$
      a. Compute nodal velocities
              $$ (v_I)^t = ((mv)_I)^t /over (m_I)^t   $$
      a. Compute gradient velocity
              $$   $$
      a. Compute gradient deformation tensor
              $$   $$
      a. Update volume
              $$   $$
      a. Update stress
              $$ (\sigma_p)^t = (\sigma_p)^t + \delta (\sigma_p)^t   $$
      a. Compute external force
              $$ (f_I)^(ext,t)  $$
      a. Compute internal force
              $$ (f_I)^(int,t) = -\Sigma V_p \sigma_p \grad N_I (x_p) $$
      a. Compute nodal force
              $$ f_I = (f_I)^(ext) + (f_I)^(int)  $$
   
   I. Update the Momenta at nodes
              $$((mv)_I)^(t+\delta t) = ((mv)_I)^t + f_I \delta t$$
   
   I. Mapping from nodes to particles
      a. Update particle velocities
         i. Normal Implementation
         i. Newmark Integration

      a. Update particle positions
         i. Normal Implementation
         i. Newmark Integration

1. Reset the grid (if it was updated) and advance to the next time step

> **Note** MPM stores information at the particles and not at the nodes as it is in Finite Element Method.

## Nomenclature



# Reference

Nguyen, V. P. (2014). "Material point method: basics and applications", Cardiff University.

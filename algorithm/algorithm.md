# MPM Algorithm

## Main Algorithm

The Material Point Method (MPM) algorithm comprises of 3 major parts.

> **Note** This method computes particle mass in the initialization. Even though the density is updated later, the mass is conserved.

1. Initialization
    1. Read all input files and store necessary information
    1. Compute mass of each particle $$ m_I = \gamma V_I $$
1. Solution phase for time step $t$ to $t + \delta t$
    1. Compute 
    1. Compute nodal mass $$ ((mv)_I)^t = \Sigma_p N_I(x_p)^t M_p $$
    1. Compute nodal momentum $$ ((mv)_I)^t = \Sigma_p N_I(x_p)^t ((Mv)_p)^t $$
    1. Compute nodal velocities $$ (v_I)^t = ((mv)_I)^t /over (m_I)^t   $$
    1. Compute gradient velocity $$   $$
    1. Compute gradient deformation tensor $$   $$
    1. Update volume $$   $$
    1. Update stress $$ (\sigma_p)^t = (\sigma_p)^t + \delta (\sigma_p)^t   $$
    1. Compute external force $$ (f_I)^(ext,t)  $$
    1. Compute internal force $$ (f_I)^(int,t) = -\Sigma V_p \sigma_p \grad N_I (x_p) $$
    1. Compute nodal force $$ f_I = (f_I)^(ext) + (f_I)^(int)  $$
    1. Update the Momenta at nodes $$((mv)_I)^(t+\delta t) = ((mv)_I)^t + f_I \delta t$$
    1. Update particle velocities  
        1. Normal Implementation
        1. Newmark Integration
    1. Update particle positions
        1. Normal Implementation
        1. Newmark Integration
1. Reset the grid (if it was updated) and advance to the next time step
1. Generate output files (.vtk) for each sub time step

> **Note** This MPM code is implementing Update Stress First (USF). The other option is to do Update Stress Last (USL). It is done by moving this block of code to the bottom after the particle velocities and displacements have been updated.


> **Note** MPM stores information at the particles and not at the nodes as it is in Finite Element Method.

## Nomenclature

# Reference

Nguyen, V. P. (2014). "Material point method: basics and applications", Cardiff University.

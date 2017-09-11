# MPM Algorithm

## Main Algorithm

The Material Point Method (MPM) algorithm comprises of 3 major parts.

1. Initialization
    a. Read all input files and store necessary information
    a. Compute mass of each particle $$ m_I = \gamma V_I $$
> **Note** This method computes particle mass in the initialization. Even though the density is updated later, the mass is conserved.
1. Solution phase for time step $t$ to $t + \delta t$
    a. Compute 
    a. Compute nodal mass $$ ((mv)_I)^t = \Sigma_p N_I(x_p)^t M_p $$
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

> **Note** This MPM code is implementing Update Stress First (USF). The other option is to do Update Stress Last (USL). It is done by moving this block of code to the bottom after the particle velocities and displacements have been updated.


    h. Compute external force
        $$ (f_I)^(ext,t)  $$

    i. Compute internal force
        $$ (f_I)^(int,t) = -\Sigma V_p \sigma_p \grad N_I (x_p) $$

    j. Compute nodal force
        $$ f_I = (f_I)^(ext) + (f_I)^(int)  $$
   
    k. Update the Momenta at nodes
        $$((mv)_I)^(t+\delta t) = ((mv)_I)^t + f_I \delta t$$
   
    l. Update particle velocities
        
        i. Normal Implementation

        ii. Newmark Integration

    m. Update particle positions

        i. Normal Implementation

        ii. Newmark Integration

1. Reset the grid (if it was updated) and advance to the next time step
1. Generate output files (.vtk) for each sub time step

> **Note** MPM stores information at the particles and not at the nodes as it is in Finite Element Method.

## Nomenclature

# Reference

Nguyen, V. P. (2014). "Material point method: basics and applications", Cardiff University.

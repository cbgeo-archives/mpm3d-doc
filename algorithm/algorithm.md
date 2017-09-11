# MPM Algorithm

## Main Algorithm

The Material Point Method (MPM) algorithm comprises of 3 major parts.

1. Initialization

    1. Read all input files and store necessary information

    1. Compute mass of each particle
        $$ M_p = \gamma V_p $$

1. Solution phase for time step $t$ to $t + \delta t$

    1. Mapping from particles to nodes 

        1. Compute nodal mass 
            $$ m_I^t = \Sigma_p N_I(x_p^t) M_p $$

        1. Compute nodal momentum
            $$ mv_I^t = \Sigma_p N_I(x_p^t) Mv_p $$

        1. Compute nodal velocities
            $$ v_I^t = \frac{mv_I^t}{m_I^t} $$

        1. Compute strain
            $$ (\varepsilon_I^t) = \Sigma_I B_I v_I $$

        1. Update stress ($d\sigma_I$ depends on constitutive model)
            $$ \sigma_I^{t+1} = \sigma_I^t + d\sigma_I $$

        1. Assign force to nodes
            $$ f_I = \Sigma_p M_p a_p  $$

        1. Compute external force from traction and body force
            $$ (f_I)^{ext,t} $$

        1. Compute internal force
            $$ (f_I)^{int,t} $$

        1. Compute nodal force
            $$ f_I = (f_I)^{ext} + (f_I)^{ext}  $$

        1. Compute nodal acceleration
            $$ a_I = f_I / m_I $$

        1. Compute gradient velocity
            $$   $$

        1. Compute gradient deformation tensor
            $$   $$

        1. Update volume
            $$   $$

    1. Update the Momenta at nodes
        $$((mv)_I)^(t+\delta t) = ((mv)_I)^t + f_I \delta t$$
   
    1. Mapping from nodes to particles

        1. Update particle velocities
        
            1. Normal Implementation

            1. Newmark Integration

        1. Update particle positions

            1. Normal Implementation

            1. Newmark Integration

1. Reset the grid (if it was updated) and advance to the next time step

1. Generate output files (.vtk) for each sub time step

> **Note** MPM stores information at the particles and not at the nodes as it is in Finite Element Method.

> **Note** This method computes particle mass in the initialization. Even though the density is updated later, the mass is conserved.

> **Note** This MPM code is implementing Update Stress First (USF). The other option is to do Update Stress Last (USL). It is done by moving this block of code to the bottom after the particle velocities and displacements have been updated.


## Nomenclature

$a_I$ acceleration of particle $I$

$a_p^t$ acceleration of particle $p$ at time $t$

$B_I$ gradient of the shape function such that $B = \fract{dN}{d\textbf{x}}$

$m_I^t$ mass of node $I$ at time $t$

$M_p^t$ mass of particle $p$ at time $t$

$mv_I^t$ momentum of node $I$ at time $t$

$Mv_p^t$ momentum of particle $p$ at time $t$

$N_I$ shape function with independent variable $\textbf{x}_p^t$

$v_I^t $ velocity of node $I$ at time $t$

$v_p^t$ velocity of particle $p$ at time $t$ 

$V_p$ volume at particle $p$

$\textbf{x}_p^t$ coordinate vector of particle $p$ at time $t$

$\gamma$ density of particle

$\boldsymbol{\varepsilon}_I^t$ strain of node $I$ at time $t$
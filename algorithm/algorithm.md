# MPM Algorithm

## Main Algorithm

The Material Point Method (MPM) algorithm comprises of 3 major parts.

1. Initialization

    1. Read all input files and store necessary information

    1. Compute mass of each particle
        $$ M_p = \gamma V_p $$

1. Solution phase for time step $t$ to $t + \delta t$

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

    1. Assign force to nodes from previous step 
        $$ f_I = \Sigma_p M_p a_p  $$

    1. Compute traction at nodes from particles
        $$ t_I^t = \Sigma_p N_I \frac{m_I^t}{\gamma} \frac{t_p^t}{spacing_p} 

    1. Compute body force at nodes from particles
       $$ b_I^t = \Sigma_p N_I M_p^t G $$

    1. Compute external force
       $$ (f_I)^{ext,t} = b_I + t_I $$

    1. Compute internal force
       $$ (\textbf{f}_I)^{int,t} = \Sigma_I \frac{m_I^t}{\gamma} B_I $\boldsymbol{\sigma}_I $$

    1. Compute nodal force
       $$ \textbf{f}_I^t = (\textbf{f}_I)^{int,t} + (\textbf{f}_I)^{ext,t}  $$

    1. Compute nodal acceleration
       $$ \textbf{a}_I^t = \textbf{f}_I^t / m_I^t $$

    1. Compute nodal velocity (normal integration). Note that Newmark integration could also be used
       $$ \textbf{v}_I^{t+1} = \textbf{v}_I^{t} + dt \textbf{a}_I^{t+1}

    1. Update soil density
        $$ \gamma = \frac{\gamma}{1 + \varepsilon_v} $$

    1. Update particle velocities. Note that Newmark integration could also be used
        $$ \textbf{v}_p^{t+1} = \textbf{v}_p^t + dt N_I \textbf{a}_I^{t+1} $$

    1. Update particle positions. Note that Newmark integration could also be used
        $$ \textbf{x}_p^{t+1} = \textbf{x}_p^t + dt N_I \textbf{v}_I^{t+1} $$

1. Reset the grid (if it was updated) and advance to the next time step

1. Generate output files (.vtk) for each sub time step

> **Note** MPM stores information at the particles and not at the nodes as it is in Finite Element Method. This includes the application of traction at the particles, not at the nodes.

> **Note** This method computes particle mass in the initialization. Even though the density is updated later, the mass is conserved.

> **Note** This MPM code is implementing Update Stress First (USF). The other option is to do Update Stress Last (USL). It is done by moving this block of code to the bottom after the particle velocities and displacements have been updated.


## Nomenclature

$\textbf{a}_I^t$ acceleration of particle $I$

$\textbf{a}_p^t$ acceleration of particle $p$ at time $t$

$\textbf{b}_I^t$ body force of node $I$

$B_I$ gradient of the shape function such that $B = \frac{dN}{d\textbf{x}}$

$\textbf{f}_I^t$ nodal force of node $I$ at time $t$

$\textbf{f}_I^{ext,t}$ nodal external force of node $I$ at time $t$

$\textbf{f}_I^{int,t}$ nodal internal force of node $I$ at time $t$

$G$ gravity acceleration ($G = 9.81 m/s^2$)

$m_I^t$ mass of node $I$ at time $t$

$M_p^t$ mass of particle $p$ at time $t$

$mv_I^t$ momentum of node $I$ at time $t$

$Mv_p^t$ momentum of particle $p$ at time $t$

$N_I$ shape function with independent variable $\textbf{x}_p^t$

$spacing_p$ spacing between particles

$\textbf{t}_I^t$ traction at node $I$

$\textbf{t}_p^t$ traction at particle $p$

$\textbf{v}_I^t $ velocity of node $I$ at time $t$

$\textbf{v}_p^t$ velocity of particle $p$ at time $t$ 

$V_p$ volume at particle $p$

$\textbf{x}_p^t$ coordinate vector of particle $p$ at time $t$

$\gamma$ density of particle

$\boldsymbol{\varepsilon}_I^t$ strain of node $I$ at time $t$
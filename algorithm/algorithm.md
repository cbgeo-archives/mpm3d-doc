# MPM Algorithm

## Main Algorithm

The Material Point Method (MPM) algorithm comprises of 4 major parts.

> **Note** 
> * MPM stores information at the material points and not at the nodes as it is in Finite Element Method. This includes the application of traction at the particles, not at the nodes.
> * The code is implementing Modified Update Stress Last (MUSF). The other option is to do Update Stress First (USF) or to do traditional Update Stress last. It is done by moving this block of code to the bottom after the material points velocities and displacements have been updated.
> * The code computes material points mass in the initialization. Even though the density is updated later, the mass is conserved.

1. Initialization

    1. Read all input files and store necessary information

    1. Compute mass of each material point
        $$ m_p = \gamma V_p $$

1. Solution phase for time step $t$ to $t + \Delta t$

    1. Compute nodal mass 
        $$ m_i^{t+\Delta t} = \Sigma_p N_ip(\textbf{x}_p^t) m_p $$

    1. Compute nodal momentum
        $$ m\textbf{v}_i^{t+\Delta t} = \Sigma_p N_ip(\textbf{x}_p^t) m\textbf{v}_p $$

    1. Compute nodal velocities
        $$ \textbf{v}_i^{t+\Delta t} = \frac{m\textbf{v}_i^{t+\Delta t}}{m_i^{t+\Delta t}} $$

    1. Compute strain from previous time-step
        $$ \boldsymbol{\varepsilon}_p^t = \Sigma_i B_ip(\textbf{x}_p^t) \textbf{v}_i^t $$

    1. Update stress from previous time-step ($\Delta\sigma_p^t$ depends on constitutive model)
        $$ \boldsymbol{\sigma}_p^t = \boldsymbol{\sigma}_p^{t-\Delta t} + \Delta \boldsymbol{\sigma}_p^t $$

    1. Assign force to nodes from previous step (for Newmark integration)
        $$ \textbf{f}_i^t = \Sigma_p N_ip(\textbf{x}_p^t) m_p \textbf{a}_p^t  $$

    1. Compute nodal acceleration from previous step (for Newmark integration)
        $$ \textbf{a}_i^t = \frac{\textbf{f}_i^{t}}{m_i^t} $$

    1. Compute body force at nodes from material points
        $$ \textbf{b}_i^{t+\Delta t} = \Sigma_p N_ip(\textbf{x}_p^t) m_p G $$

    1. Compute traction at nodes from material points
        $$ \textbf{t}_i^{t+\Delta t} = \Sigma_p N_ip(\textbf{x}_p^t) \frac{m_I^{t+\Delta t}}{\gamma} \frac{\textbf{t}_p^{t+\Delta t}}{s_p} $$

    1. Compute external force
        $$ (\textbf{f}_i)^{ext,t+\Delta t} = \textbf{b}_i^{t+\Delta t} + \textbf{t}_i^{t+\Delta t} $$

    1. Compute internal force
        $$ (\textbf{f}_i)^{int,t+\Delta t} = \Sigma_i \frac{m_i^{t+\Delta t}}{\gamma} B_ip \boldsymbol{\sigma}_i^{t+\Delta t} $$

    1. Compute nodal force
        $$ \textbf{f}_i^{t+\Delta t} = (\textbf{f}_i)^{int,t+\Delta t} + (\textbf{f}_i)^{ext,t+\Delta t}  $$

    1. Compute nodal acceleration
        $$ \textbf{a}_i^{t+\Delta t} = \frac{\textbf{f}_i^{t+\Delta t}}{m_i^t} $$

    1. Compute nodal velocity
        1. Normal Integration
            $$ \textbf{v}_i^{t+\Delta t} = \textbf{v}_i^{t} + \Delta t \textbf{a}_i^{t+\Delta t} $$
        1. Newmark Integration
            $$ \textbf{v}_i^{t+\Delta t} = \textbf{v}_i^{t} + \Delta t (1-\gamma_N) \textbf{a}_i^t + \Delta t \gamma_N \textbf{a}_i^{t+\Delta t} $$

    1. Update soil density
        $$ \gamma = \frac{\gamma}{1 + \varepsilon_{v,p}} $$

    1. Update material points acceleration
        $$ \textbf{a}_p^{t+\Delta t} = \Sigma_i N_(ip)(\textbf{x}_p^t) \textbf{a}_i^{t+\Delta t} $$

    1. Update material points velocity
        1. Mapping from nodal velocity
            $$ \textbf{v}_p^{t+\Delta t} = \Sigma_i N_i \textbf{x}_p^t) \textbf{v}_i^{t+\Delta t} $$

        1. Normal Integration
            $$ \textbf{v}_p^{t+\Delta t} = \textbf{v}_p^t + \Delta t  \textbf{a}_I^{t+\Delta t} $$
        1. Newmark Integration
            $$ \textbf{v}_p^{t+\Delta t} = \textbf{v}_p^{t} + (1-\gamma_N) \Delta t \textbf{a}_p^t + \gamma_N \Delta t \textbf{a}_p^{t+\Delta t} $$

    1. Update material points position
        1. Normal Integration
            $$ \textbf{x}_p^{t+\Delta t} = \textbf{x}_p^t + \Delta t \textbf{v}_p^{t+\Delta t} $$
        1. Newmark Integration
            $$ \textbf{x}_p^{t+\Delta t} = \textbf{x}_p^t + \Delta t \textbf{v}_p^t + \frac{1-2\beta_N}{2} {\Delta t}^2 \textbf{a}_p^t + \beta_N {\Delta t}^2 \textbf{a}_p^{t+\Delta t} $$

1. Reset the grid mesh (if it was updated) and advance to the next time step

1. Generate output files (.vtk) for each sub time step  


## Nomenclature

$\textbf{a}_i^t$ acceleration of node $i$

$\textbf{a}_p^t$ acceleration of material point $p$ at time $t$

$\textbf{b}_i^t$ body force of node $i$

$B_{ip} (\textbf{x}_p^t)$ gradient of the shape function that maps node $i$ to material point $p$ and vice versa such that $B = \frac{dN}{d\textbf{x}}$

$\textbf{f}_i^t$ nodal force of node $i$ at time $t$

$\textbf{f}_i^{ext,t}$ nodal external force of node $i$ at time $t$

$\textbf{f}_i^{int,t}$ nodal internal force of node $i$ at time $t$

$G$ gravity acceleration ($G = 9.81 m/s^2$)

$m_i^t$ mass of node $i$ at time $t$

$m_p^t$ mass of material point $p$ at time $t$

$m\textbf{v}_i^t$ momentum of node $i$ at time $t$

$m\textbf{v}_p^t$ momentum of material point $p$ at time $t$

$N_{ip} (\textbf{x}_p^t) $ shape function that maps node $i$ to material point $p$ and vice versa with independent variable of the location of each material point at time $t$

$s_p$ spacing between material points

$\textbf{t}_i^t$ traction at node $i$

$\textbf{t}_p^t$ traction at material point $p$

$\textbf{v}_i^t $ velocity of node $i$ at time $t$

$\textbf{v}_p^t$ velocity of material point $p$ at time $t$ 

$V_p$ volume at material points $p$

$\textbf{x}_p^t$ coordinate vector of material point $p$ at time $t$

$\beta_N$ parameter for Newmark integration

$\gamma$ density of material point

$\gamma_N$ parameter for Newmark integration

$\varepsilon_{v,p}^t$ volumetric strain of particle $p$ at time $t$

$\boldsymbol{\varepsilon}_i^t$ strain of node $i$ at time $t$

$\boldsymbol{\sigma}_i^t$ stress of node $i$ at time $t$
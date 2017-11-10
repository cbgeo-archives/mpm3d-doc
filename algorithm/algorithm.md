# MPM Algorithm

The UCAM-MPM3D code uses Modified Update Stress Last (MUSL) algorithm. MUSL
calculates the nodal velocities from material point momentums at the end of the time step, i.e. before updating material points position.

The Material Point Method (MPM) algorithm comprises of 4 stages.

1. Initialisation

    1. A continuum body is discretised into a finite set of material points corresponding to the original configuration of the body. The number of material points corresponds to the resolution of the mesh size adopted in the Finite Element Method. The material points are followed throughout the deformation of the material, which is a Lagrangian description of the motion.

    1. An arbitrary computational grid is initialised to describe the natural coordinates of the material points. For the purpose of simplicity, a Cartesian grid is usually adopted.

    1. The state variables (mass/density, velocity, strain, stress, other material parameters corresponding to the adopted constitutive relation) are initialised at every material point.

    1. Mass of each material point is computed based on the density and initial volume. The initial volume is computed based on the material point spacing. The code assumes uniform spacing in all directions. Although the density of material points are updated, the mass is conserved.

        $$ m_p = \rho V_p $$

1. Solution phase for time step $$t$$ to $$t + \Delta t$$

    1. Compute nodal mass
        $$ m_i^t = \sum\limits_{p=1}^{n_P} N_i(\textbf{x}_p^t) m_p $$

    1. Compute nodal momentum     
        $$ (m \textbf{v})_i^{t} = \sum\limits_{p=1}^{n_P} N_i(\textbf{x}_p^t) m_p \textbf{v}_p^{t} $$

    1. Compute nodal velocities
        $$ \textbf{v}_i^{t} = \frac{(m\textbf{v})_i^{t}}{m_i^{t}} $$

    1. Assign force to nodes from previous step (for Newmark integration)
        $$ \textbf{f}_i^t = \sum\limits_{p=1}^{n_P} N_i(\textbf{x}_p^t) m_p \textbf{a}_p^t  $$

    1. Compute nodal acceleration from previous step (for Newmark integration)
        $$ \textbf{a}_i^t = \frac{1}{m_i^t} \textbf{f}_i^{t} $$

    1. Compute strain from previous time-step $$t$$
        $$ \boldsymbol{\varepsilon}_p^t = \sum\limits_{p=1}^{n_P} B_i(\textbf{x}_p^t) \textbf{v}_i^t $$

    1. Update stress from previous time-step $$t$$ ($$\Delta\sigma_p^t$$ depends on the constitutive model)
        $$ \boldsymbol{\sigma}_p^t = \boldsymbol{\sigma}_p^{t-\Delta t} + \Delta \boldsymbol{\sigma}_p^t $$

        $$ \Delta\boldsymbol{\sigma}_p^t= \mathbf{D} : \Delta \boldsymbol{\varepsilon}_p^t $$

    1. Compute body force at nodes from material points
        $$ \textbf{b}_i^{t} = G \sum\limits_{p=1}^{n_P} N_i(\textbf{x}_p^t) m_p $$

    1. Compute traction at nodes from material points
        $$ \textbf{t}_i^{t} = \sum\limits_{p=1}^{n_P} N_i(\textbf{x}_p^t) \frac{m_i^{t}}{\gamma} \frac{1}{s_p} \textbf{t}_p^{t} $$

    1. Compute external force
        $$ (\textbf{f}_i)^{ext,t} = \textbf{b}_i^{t} + \textbf{t}_i^{t} $$

    1. Compute internal force
        $$ (\textbf{f}_i)^{int,t} = \sum\limits_{p=1}^{n_P} \frac{m_i^{t}}{\gamma} B_i(\textbf{x}_p^t) \boldsymbol{\sigma}_i^{t} $$

    1. Compute nodal force
        $$ \textbf{f}_i^{t} = (\textbf{f}_i)^{int,t+\Delta t} + (\textbf{f}_i)^{ext,t+\Delta t}  $$

    1. Compute nodal acceleration
        $$ \textbf{a}_i^{t} = \frac{1}{m_i^t} \textbf{f}_i^{t}$$

    1. Compute nodal velocity
        1. Normal Integration
            $$ \textbf{v}_i^{t+\Delta t} = \textbf{v}_i^{t} + \Delta t \textbf{a}_i^{t} $$

        1. Newmark Integration
            $$ \textbf{v}_i^{t+\Delta t} = \textbf{v}_i^{t} + \Delta t (1-\gamma_N) \textbf{a}_i^t + \Delta t \gamma_N \textbf{a}_i^{t+\Delta t} $$

    1. Update soil density
        $$ \rho = \frac{\rho}{1 + \varepsilon_{v,p}} $$

    1. Update material points acceleration
        $$ \textbf{a}_p^{t} = \Sigma_i N_i(\textbf{x}_p^t) \textbf{a}_i^{t} $$

    1. Update material points velocity
        1. Mapping from nodal velocity
            $$ \textbf{v}_p^{t+\Delta t} = \Sigma_i N_i(\textbf{x}_p^t) \textbf{v}_i^{t+\Delta t} $$

        1. Normal Integration
            $$ \textbf{v}_p^{t+\Delta t} = \textbf{v}_p^t + \Delta t  \textbf{a}_p^{t} $$

        1. Newmark Integration
            $$ \textbf{v}_p^{t+\Delta t} = \textbf{v}_p^{t} + (1-\gamma_N) \Delta t \textbf{a}_p^t + \gamma_N \Delta t \textbf{a}_p^{t+\Delta t} $$

    1. Update material points position
        1. Normal Integration
            $$ \textbf{x}_p^{t+\Delta t} = \textbf{x}_p^t + \Delta t \textbf{v}_p^{t+\Delta t} $$

        1. Newmark Integration
            $$ \textbf{x}_p^{t+\Delta t} = \textbf{x}_p^t + \Delta t \textbf{v}_p^t + \frac{1-2\beta_N}{2} {\Delta t}^2 \textbf{a}_p^t + \beta_N {\Delta t}^2 \textbf{a}_p^{t+\Delta t} $$

1. At the end of every time step, all the variables on the grid nodes are initialised to zero. The material points carry all the information about the solution, and the computational grid is re-initialised for the next step.

1. Generate output files (.vtk) for each sub time step  

> **Note**
> * MPM stores information at the material points and not at the nodes as it is in the Finite Element Method. This includes the application of traction at the material points, not at the nodes.


## Nomenclature

### General

$$G$$ acceleration due to gravity ($$G = 9.81 m/s^2$$)

$$\beta_N$$ parameter for Newmark integration

$$\gamma_N$$ parameter for Newmark integration


### Material Point

$$\textbf{a}_p^t$$ acceleration of material point $$p$$ at time $$t$$

$$m_p^t$$ mass of material point $$p$$ at time $$t$$

$$m\textbf{v}_p^t$$ momentum of material point $$p$$ at time $$t$$

$$n_p$$ total number of material points in the body

$$s_p$$ spacing between material points

$$\textbf{t}_p^t$$ traction at material point $$p$$

$$\textbf{v}_p^t$$ velocity of material point $$p$$ at time $$t$$

$$V_p$$ volume at material points $$p$$

$$\textbf{x}_p^t$$ coordinate vector of material point $$p$$ at time $$t$$

$$\varepsilon_{v,p}^t$$ volumetric strain of particle $$p$$ at time $$t$$

$$\rho$$ density of material point


### Node

$$\textbf{a}_i^t$$ acceleration of node $$i$$

$$\textbf{b}_i^t$$ body force of node $$i$$

$$\textbf{f}_i^t$$ nodal force of node $$i$$ at time $$t$$

$$\textbf{f}_i^{ext,t}$$ nodal external force of node $$i$$ at time $$t$$

$$\textbf{f}_i^{int,t}$$ nodal internal force of node $$i$$ at time $$t$$

$$m_i^t$$ mass of node $$i$$ at time $$t$$

$$m\textbf{v}_i^t$$ momentum of node $$i$$ at time $$t$$

$$\textbf{t}_i^t$$ traction at node $$i$$

$$\textbf{v}_i^t$$ velocity of node $$i$$ at time $$t$$

$$\boldsymbol{\varepsilon}_i^t$$ strain of node $$i$$ at time $$t$$

$$\boldsymbol{\sigma}_i^t$$ stress of node $$i$$ at time $$t$$


### Shape Functions

$$B_i (\textbf{x}_p^t)$$ gradient of the shape function that maps node $$i$$ to material point $$p$$ and vice versa such that $$B = \frac{dN}{d\textbf{x}}$$

$$N_i (\textbf{x}_p^t)$$ shape function that maps node $$i$$ to material point $$p$$ and vice versa with independent variable of the location of each material point at time $$t$$

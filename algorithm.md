# MPM Algorithm

## Main Algorithm

The Material Point Method (MPM) algorithm comprises of 3 major parts.

1. Initialization

2. Mapping from particles to nodes
       a.

3. Update the Momenta at nodes

$((mv)_I)^(t+\delta t) = ((mv)_I)^t + f_I \delta t$

$$\sum_{\forall i}{x_i^{2}}$$

When $$a \ne 0$$, there are two solutions to $$(ax^2 + bx + c = 0)$$ and they are $$x = {-b \pm \sqrt{b^2-4ac} \over 2a}.$$




4. Mapping from nodes to particles

5. Reset the grid (if it was updated) and advance to the next time step

> **Note** MPM stores information at the particles and not at the nodes as it is in Finite Element Method.

## Nomenclature



# Reference

Nguyen, V. P. (2014). "Material point method: basics and applications", Cardiff University.

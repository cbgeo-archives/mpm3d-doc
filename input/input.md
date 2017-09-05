# Preprocessing

## Units
A consistent system of units must be maintained in this code. All values input into the code with units must follow a system from the table in the following link:

http://www.idac.co.uk/services/downloads/consistent.pdf.


## Dimensions
To switch between the 2D and 3D implementation of `mpm-3d` code, uncomment or comment `#define _MPM2D_ ` in [MpmItems.hpp](https://github.com/cb-geo/mpm-3d/blob/master/cartesian/src/MpmItems.hpp#L15). `#define _MPM2D_` enables 2D, uncommenting the line enables the 3D version.

## Directory

Each new problem must have a new folder (eg.`element_testing`) within this folder the input files must be located in a seperate folder entitiled `inputFiles`. By default, problem folders are assumed to be located in a `bin` folder, giving `bin/element_testing/inputFiles`. The command `./mpm -f element_testing` will direct the code to the `element_testing` folder and run the input files located within it.

`mpm-3d` requires a collection of input files, a description of each is provided below.


# Input Files

## Input.dat

An `input.dat` file provides the location of the other necessary files.

An example of the requirsamaed format is given below for a problem titled `element_testing`.

```
inputMeshFileName              element_testing/inputFiles/mesh.smf
inputSubMeshFileName           element_testing/inputFiles/submesh.dat
constraintsFileName            element_testing/inputFiles/mesh.constraints
inputSoilParticleFileName      element_testing/inputFiles/soilParticles.dat
initStressSoilPFileName        element_testing/inputFiles/initStressSoilP.dat
materialFileName               element_testing/inputFiles/material.dat
tractionsSoilPFileName         element_testing/inputFiles/Traction.dat
outputFileName                 element_testing/results/results1.ile
```

Therefore, the code would open `element_testing/inputFiles/mesh.smf` to find the input mesh file required.

The `input.dat` file also contains key parameters required to set the conditions of the model they are as follows:

```
gravityFlag                    1
boundaryFrictionMiu            0
soilParticleSpacing            0.005
timeInterval                   1e-06
numberOfSteps                  800000
numberOfSubStepsOS             1000
newmarkMethod                  0
gamma                          0.5
beta                           0.25
dampingFlag                    0
dampingRatio                   0.05
```

`gravityFlag` determines if gravity is on or off. `1` is on, `0` is off.

`boundaryFrictionMiu` determines the friction coefficient, ranging from `0` to `1`.

`timeInterval` determines the time-step in seconds and `numberOfSteps` determines the number of steps. `numberOfSubStepsOS ` is the number of substeps.


### Spacing

`soilParticleSpacing` is used to calculate the mass of a point, using: `mass = soilParticleSpacing  * soilParticleSpacing  * soilParticleSpacing  * density` for a 3D model. 

Note: Spacing must have units concordant with those used for density and the geometry of the model, the density input is described later.


### Newmark Integration

Setting `newmarkMethod` as 1 will integrate velocity and displacement using the Newmark-beta method (1959). See [Newmark Beta method](https://en.wikipedia.org/wiki/Newmark-beta_method).
.
In this method, two extra parameters are required, i.e., `gamma` and `beta`. A reasonable value of `gamma` is 0.5 and `beta` can be anywhere between 0 and 0.5 with a recommended value of 0.25 according to the above reference.

### Damping

To reduce stress oscillation in this explicit code, artificial damping is implemented. Setting `dampingFlag` as 1 will add damping to the equation of motion.

A `dampingRatio` parameter is required with a recommended value is between 0.02 and 0.05.

This damping method is usually known as [Cundall damping](https://elib.uni-stuttgart.de/handle/11682/513).


## Mesh

In MPM, the background mesh is an important component of the method. Data stored in the points are mapped to the nodes of the mesh where computations are performed.

Note that a mesh generator code is usually used to make the mesh files inputs including the submesh and the constraints. Note that `cb-geo` has a [mesh generator](https://github.com/cb-geo/mpm-mesh-generator-old) that is usually used.

### Mesh File

The mesh file must have the following format:

```
NN   NE
x1  y1  xNN
....
z1 yNN zNN
n1_1  n1_2 ... n1_NPE
...
nNE_1 nNE_2 ... nNE_NPE 

```

`NN` is the number of nodes.

`NE` the number of elements.
 
`x_i, y_i,z_i` correspond to the Cartesian coordinates of each node. 

The `ID` of each node from `0-NN` will be the order in which they written, e.g.:


```
x1 y1 z1 = Node 0
x2 y2 z2 = Node 1
...
xNN yNN zNN = Node NN

```

`NPE` refers to the number of nodes per element. 


`n1_1, n1_2... n1_NPE` correspons to which nodes make up each element using the unwritten Node `ID`'s.

An example of the single element `element_testing/inputFiles/mesh.smf` mesh file is given below.

```
! elementShape quadrilateral 
! elementNumPoints 4 
4 1
0 0 0 //Node coordinates
1 0 0
0 1 0
1 1 0
0 1 3 2 //Element made of Node ID's
```

Note that for 2D element, the element shape will be `quadrilateral`. For 3D element, it will be `hexahedron`. Assuming element vertices are nodes, `quadrilateral` elements will have 4 nodes and `hexahedron` 8 nodes.

### Submesh
A SubMesh is a mesh defined as a subset of a given mesh. It provides a convenient way to create matching meshes for multiphysics applications by creating meshes for subdomains as subsets of a single global mesh.

In the example of element testing, there would not be any submesh used. Therefore, the file `element_testing/inputFiles/submesh.dat` would look as follow.

```
1
0 0 0 0 0
```

### Constraints

The boundary constraints are specified and the file has to have the following format:

```
NGC     NFC
n_1     d_1
...
n_NGC   d_NGC
n_1     dn_1     sdn_1
...
n_NFC   dn_NFC   sdn_NFC

```


`NGC` is the number general constraints set on nodes within in this file. General constraints are used to set velocity and acceleration components in the `d_i` direction to zero.
This will reduce point movement in the `d_i` direction the closer it is to that node.

`NFC` is the number of frictional constraints set on nodes within this file. These nodes are used to limit the acceleration in the tangential direction to boundary using the frictional algorithm and the fricional coefficiant `boundaryFrictionMiu`.

`n_i` is the node number.

`d_i` is the direction number `(0|1|2)` that corresponds to a cartesian direction, typically `(x|y|z)`.

`dn_i` is the perpendicular direction to friction boundary in the form of the direction number `(0|1|2)`. 

`sdn_i` is the `sign` `(+1/-1)` of `dn_i` with reference to the coordinate syste (i.e.=-1 if bottom of mesh boundary)

An example of the `element_testing/inputFiles/mesh.constraints` is shown below.

```
4 4
0 0
2 0
0 1
1 1
2 1 1
3 1 1
1 0 1
3 0 1
```
In this case, there are 4 general constraints set on nodes and 4 frictional constraints. Multiple constraints can be set on nodes, for instance: `Node 0` has constraints in both the `0` direction and the `1` direction. 


## Soil Particles (Points) File

The the geometry (location) of points to be used in the model must be set in a file pointed to by the `inputSoilParticleFileName` header of the `input.dat` file. 

This file is typically generated by an outside code, but must contain the following format:

```
Np
x_1 y_1 z_1
...
x_Np y_Np z_Np

```

Where Np is the total number of points and `x_i y_i z_i` corresponds to the cartesian coordinates of each point.

For a 2D case, the `z_i` coordinate must be set to 0.

`cb-geo` has a [point generator](https://github.com/cb-geo/mpm-point-generator) that is usually used.

Note: The best practice for point locations is to avoid points being located on the nodes of the background mesh or element boundaries.

An example of the `element_testing/inputFiles/soilParticles.dat` file is shown below.

```
4
0.211324865	0.211324865	0
0.788675135	0.211324865	0
0.211324865	0.788675135	0
0.788675135	0.788675135	0
```

In this file there are a total of 4 points.


Note: The order in which the points are written corresponds to their `Point ID`, which is used to calculate stresses. 



### Initial Stress

The 6 component Voigt Stress Notation is being used in this file. More information can be found in [Voight Notation](https://en.wikipedia.org/wiki/Voigt_notation).

This file is typically generated by an outside code, but must contain the following format:

```
Np
ID_1 s1_1 s2_1 s3_1 s4_1 s5_1 s6_1
...
ID_Np s1_Np s2_Np s3_Np s4_Np s5_Np s6_Np

```

Where `Np` is the number of nodes and `s1_i s2_i s3_i s4_i s5_i s6_i` is the Voigt stress notation of `sigma 1-6`.

`ID_i` corresponds to the unwritten `Point ID` of each point, as determined by the order in which they are written in the `Soil Particles` file.


An example of the `element_testing/inputFiles/initStressSoilP.dat` file is shown below.

```
4
0	0	0	0	0	0	0	
1	0	0	0	0	0	0	
2	0	0	0	0	0	0	
3	0	0	0	0	0	0
```

In this case there are 4 points with no initial stresses set.

Note: That the `Point ID` must start at `0` not 1, and the `Np` of this file should match that of the `Soil Particles` file, otherwise an erorr will occur.


### Traction

In MPM, traction or external forces are applied on the points (not to nodes of the mesh like FEM). The format of the file should be like the following:

```
NTP            \n
p_1     d_1    tp_1    \n
...                    \n
p_NTP   d_NTP  tp_NTP  \n
```
where `NTP` is the number of traction particles in this file, which are used to give traction forces in the `d_i` direction. 
`p_i` is the particle id, 
`d_i` is the direction number (0|1|2) of traction force. 
`tp_i` is the traction pressure. 
`NTP` is the number of particles with traction pressures.


An example in the `element_testing/inputFiles/Traction.dat` is shown below.

```
4
0	1	-50000
1	1	-50000
2	1	-50000
3	1	-50000
```

## Material

The material model selected to run the codes need to be specified in this file: `element_testing/inputFiles/material.dat`. For instance, if isotropic linear elastic model is used, the code would look something like this:

```
IsotropicLinearElastic {
       youngModulus 1e+07
       poissonRatio 0
       phi 0
       porosity 0
       density 2038.74
}
```
Currently, there are only two models being implemented, i.e., Isotropic Linear Elastic and Mohr Coulomb. Note that every model would have different number and sets of parameters required. Mohr Coulomb, for instance, would have more parameters and an example is shown below.

```
MohrCoulomb {
       youngModulus 1e+06
       poissonRatio 0.2
       phi 28
       psi 0
       cohesion 0
       sigt 0
       porosity 0.3
       density 2038.74
}
```

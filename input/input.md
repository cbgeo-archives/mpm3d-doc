# Preprocessing

## Units
A consistent system of units must be maintained in this code. All values input into the code with units must follow a system from the table in the following link:

http://www.idac.co.uk/services/downloads/consistent.pdf.


## Dimensions
To run in 2D uncomment `line 15` (`// #define _MPM2D_  // Toggle between MPM2D and 3D Analysis`) from `MpmItems.hpp` prior to compiling the code. For 3D, leave this line commented out and compile as normal.


## Directory

Each new problem must have a new folder (eg.`element_testing`) within this folder the input files must be located in a seperate folder entitiled `inputFiles`. By default, problem folders are assumed to be located in a `bin` folder, giving `bin/element_testing/inputFiles`. The command `./mpm -f element_testing` will direct the code to the `element_testing` folder and run the input files located within it.

A series of input files are required to run the `mpm-3d` code, a description of each is provided below.


# Input Files

## Input.dat

An `input.dat` file within the `inputFiles` folder is required to provide the code with the location of the other necessary files.

An example of the required format is given below for a problem titled `element_testing`.

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

Setting `newmarkMethod` as 1 will integrate velocity and displacement using the Newmark-beta method (1959). The details of the mathematics can be seen at: https://en.wikipedia.org/wiki/Newmark-beta_method. 

In this method, two extra parameters are required, i.e., `gamma` and `beta`. A reasonable value of `gamma` is 0.5 and `beta` can be anywhere between 0 and 0.5 with a recommended value of 0.25.

### Damping

To reduce stress oscillation in this explicit code, artificial damping is implemented. Setting `dampingFlag` as 1 will add damping to the equation of motion.

A `dampingRatio` parameter is required with a recommended value is between 0.02 and 0.05.

Reference to this method could be obtained from: https://elib.uni-stuttgart.de/handle/11682/513.


## Mesh

In MPM, background mesh is one important component of the method. Data are stored in the points and mapped to the nodes of the mesh where computations are performed.

Note that a mesh generator code is usually used to make the mesh files inputs including the submesh and the constraints.

### Mesh File

The mesh file must have the following format:

```
NN   NE
x1  y1  z1
....
xNN yNN zNN
n1_1  n1_2 ... n1_NPE
...
nNE_1 nNE_2 ... nNE_NPE 

```

`NN` is the number of nodes.

`NE` the number of elements.
 
`x_i, y_i,z_i` correspond to the Cartesian coordinates of each node. 

The `ID` of each node from `0-NN` will be the order in which they written.eg:


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


## Soil Particles (Points)

Then, we need points as after all the method is called material point method for a reason. The initial location of the points should not coincide with the nodes of the mesh, neither should it be on the boundary of the elements.

Note that these files with the points are usually generated with another code. 

### Location

A file simply containing the number of points along with the coordinates. Note that even in 2D, z coordinate value needs to be specified as well.

An example in the `element_testing/inputFiles/soilParticles.dat` is shown below.

```
4
0.211324865	0.211324865	0
0.788675135	0.211324865	0
0.211324865	0.788675135	0
0.788675135	0.788675135	0
```

The first line indicates the number of points in the file, and the rest are the coordinates.

### Initial Stress

Voigt Stress Notation is being used, which has 6 components. More information could be found in this link: https://en.wikipedia.org/wiki/Voigt_notation.

An example in the `element_testing/inputFiles/initStressSoilP.dat` is shown below.

```
4
0	0	0	0	0	0	0	
1	0	0	0	0	0	0	
2	0	0	0	0	0	0	
3	0	0	0	0	0	0
```

The first entry of every line refers to the index of the point.

Lastly, in terms of the units used, please check the information regarding consistent unit system at the bottom of the document.


### Traction

In MPM, traction or external forces are applied on the points (not to nodes of the mesh like FEM). The format of the file should be like the following:

```
NTP            \n
p_1     d_1    tp_1    \n
...                    \n
p_NTP   d_NTP  tp_NTP  \n

where NTP is the number of traction particles in this file, which are used
to give traction forces in the d_i direction. 
p_i is the particle id, 
d_i is the direction number (0|1|2) of traction force. 
tp_i is the traction pressure. 
NTP is the number of particles with traction pressures.
``` 

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

## Notes




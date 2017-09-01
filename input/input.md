# Preprocessing

To run `mpm-3d` code, there are a series of input files required. 

## Directory

All the input files need to be stored in a folder entitled `inputFiles`. For example, running a problem called `element_testing` would require it to be located such that we have `bin/element_testing/inputFiles`. The results would be stored at `bin/element_testing/results`.


## Input

The code would first read 'input.dat' (directory: `bin/element_testing/inputFiles/input.dat`). This file would consist of the location to other input files as well as details of other parameters needed to run the code.

Assuming that we have the following contents in `input.dat`:

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

In addition, there are other parameters that are important in this file. Here is an example:

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

Most of the parameters are self-explanatory. There are two things to be explained:
* Newmark Integration
* Damping

### Newmark Integration

Putting `newmarkMethod` as 1 would integrate velocity and displacement using Newmark-beta method (1959). The details of the mathematics can be seen here: https://en.wikipedia.org/wiki/Newmark-beta_method. 

In this method, two extra parameters are required, i.e., `gamma` and `beta`. A reasonable value of `gamma` is 0.5 and `beta` could be anywhere between 0 and 0.5 with a recommended value of 0.25.

### Damping

To reduce oscillation in this explicit code, artificial damping is implemented. Putting `dampingFlag` as 1 would add damping to the equation of motion.

One parameter is required, i.e., `dampingRatio`. A recommended value is between 0.02 and 0.05.

Reference to this method could be obtained from: https://elib.uni-stuttgart.de/handle/11682/513.


## Mesh

In MPM, background mesh is one important component of the method. Data are stored in the points and mapped to the nodes of the mesh where computations are performed.

Note that a mesh generator code is usually used to make the mesh files inputs including the submesh and the constraints.

### Mesh File

The mesh file has to have the following format:

```
NN   NE \n
x1  y1  z1  \n
....        \n
xNN yNN zNN \n
v1_1  v1_2 ... v1_NPE   \n
...                     \n
vNE_1 vNE_2 ... vNE_NPE \n

where NN is the number of nodes, 
NE the number of elements, and 
NPE refers to the number of nodes per element. 
Note that the latter number is fixed at compile time.
```

An example in the `element_testing/inputFiles/mesh.smf` where there is only one element is provided here.

```
! elementShape quadrilateral 
! elementNumPoints 4 
4 1
0 0 0
1 0 0
0 1 0
1 1 0
0 1 3 2
```

Note that for 2D element, we would have the element shape to be `quadrilateral`. For 3D element, it would be `hexahedron`. The element number of points (nodes) for the former is 4 and for the latter is 8.

### Submesh
* Not yet written


### Constraints

The boundary constraints are specified and the file has to have the following format:

```
NGC     NFC    \n
n_1     d_1    \n
...            \n
n_NGC   d_NGC  \n
n_1     dn_1     sdn_1     \n
...                        \n
n_NFC   dn_NFC   sdn_NFC   \n

where NGC is the number of general constraints in this file, which are used to set velocity and acceleration components in the d_i direction to be zero. 
n_i is the node number, d_i is the direction number (0|1|2) NFC is the number of friction constraints, which are used to limit the acceleration in the tangential direction to boundary. 
n_i is the node number, dn_i is the perpendicular direction to boundary (0|1|2). 
sdn_i is the sign (+1/-1) of dn_i w.r.t coordinate system ( i.e. =-1 if bottom of mesh boundary )
 ```

An example in the `element_testing/inputFiles/mesh.constraints` is shown below.

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

### Units
This code uses a consistent system of units. The table in the following link provides examples of the sets of units: http://www.idac.co.uk/services/downloads/consistent.pdf.


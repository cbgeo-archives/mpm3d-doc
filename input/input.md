# Preprocessing

To run `mpm-3d` code, there are a series of input files required.  

## Directory

All the input files need to be stored in a folder entitled `inputFiles`. For example, running a problem called `element_testing` would require it to be located such that we have `bin/element_testing/inputFiles`. The results would be stored at `bin/element_testing/results`.


## Input

The code would first read 'input.dat' (directory: bin/element_testing/inputFiles/input.dat). This file would consist of the location to other input files as well as details of other parameters needed to run the code.

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

Therefore, the code would open 'element_testing/inputFiles/mesh.smf' to find the input mesh file required.

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


## Mesh

### Mesh File

### Submesh

### Constraints


## Soil Particles / Points

### Location

### Initial Stress

### Traction


## Material

The material model selected to run the codes need to be specified in this file. For instance, if isotropic linear elastic model is used, the code would look something like this:

```
IsotropicLinearElastic {
       youngModulus 1e+07
       poissonRatio 0
       phi 0
       porosity 0
       density 2038.74
}
```

Currently, there are only two models being implemented, i.e., Isotropic Linear Elastic and Mohr Coulomb.

## Notes

### Units
This code uses a consistent system of units. The table in the following link provides examples of the sets of units: http://www.idac.co.uk/services/downloads/consistent.pdf.


# Preprocessing

To run `mpm-3d` code, there are a series of input files required.  

## Directory

Note that all the input files need to be stored in a folder entitled `inputFiles`. For example, running a problem called `element_testing` would require it to be located such that we have `bin/element_testing/inputFiles`. The results would be stored at `bin/element_testing/results`.


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


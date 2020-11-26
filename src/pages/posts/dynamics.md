---
title: Dynamics
excerpt: Molecular dynamics engine based on NAMD
thumb_img_path: images/namd.png
template: post
---
# What is Molecular Dynamics?
Molecular Dynamics is the application of Newtonian mechanics to atomistic systems. The inter-atomic potential is specified by a force field that is usually calibrated from quantum mechanical calculations.

![Image of MD](https://github.com/MolSSI/MMIC_dynamics/blob/master/mmic_dynamics/data/imgs/dynamics.png?raw=true)
    

# Dynamics Component
## Preparing Input for NVE simulation

```python
# Import MM molecule data model
from mmelemental.models.molecule.mm_molecule import Molecule

# Construct MM molecule
mol = Molecule.from_file(pdb_file)

# Import dynamics input schema model
from mmelemental.models.sim.sim import DynamicsInput

# Construct docking input data from MM molecules
md_input = DynamicsInput(
	   mol=mol,
	   cell=(0,10,0,10,0,10),
	   forcefield='charmm36',
	   nstesp=10000
	)
```

## Running MD Simulation
![Image of Comp](https://github.com/MolSSI/MMIC_dynamics/blob/master/mmic_dynamics/data/imgs/md.png?raw=true)

```python
# Import docking simulation component for autodock vina
from mmcomponents_dynamics.components.namd_component import NamdComponent

# Run autodock vina
md_output = NamdComponent.compute(md_input)

# Extract trajectory
coords_final, traj = md_output.traj.result[-1].coordinates, md_output.traj
```

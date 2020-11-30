---
title: Docking
excerpt: Component for molecular docking based on Autodock Vina
template: post
thumb_img_path: images/vina-removebg-preview.png
---
# What is Docking?
Docking is a computational technique used to determine the optimal binding modes of a ligand-receptor system. A ligand is typically a small (e.g. drug) molecule that binds to a macromolecular receptor such as a protein. A docking simulation estimates the strength of the binding (or a quantitative "score") in the vicinity of the receptor's binding site. Each score corresponds to the 3D conformation and orientation (or "pose") of the ligand relative to the receptor.

![Image of Docking](https://github.com/MolSSI/MMIC_docking/blob/master/mmic_docking/data/imgs/docking-sys.png?raw=true)

Applications of docking include:

- Virtual screening (hit identification)
- Drug discovery (lead optimization)
- Binding site identification (blind docking)
- Protein-protein interactions
- Enzymatic reaction mechanisms
- Protein engineering

# Docking Component
## Preparing Input

```python
# Import molecule schema model
from mmelemental.models.molecule.mm_molecule import Molecule

# Construct molecules
receptor_data   = Molecule.from_file(pdb_file)
ligand_data     = Molecule.from_data(smiles_code)

# Import docking data model
from mmic_docking.models.docking.input import DockingInput

# Import molecule schema model
from mmelemental.models.molecule.mm_molecule import Molecule

# Construct molecules
receptor_data   = MMolecule.from_file(pdb_file)
ligand_data     = MMolecule.from_data(smiles_code)

# Import docking data model
from mmcomponents_docking.models.docking.input import DockingInput

# Construct docking input data from molecules
dock_input = DockingInput(ligand=ligand_data, receptor=receptor_data)
```

## Running Docking with AutoDock Vina component

![Image of Comp](https://github.com/MolSSI/MMIC_docking/blob/master/mmic_docking/data/imgs/autodock.png?raw=true)

```python
# Import docking simulation component for autodock vina
from mmic_docking.autodock.autodock_component import AutoDockComponent

# Run autodock vina
dock_output = AutoDockComponent.compute(dock_input)

# Extract output
scores, poses = dock_output.scores, dock_output.poses
```

# 3D-day-2
**1. Install ChimeraX**
Install ChimeraX on your local computer, following these instructions: https://www.rbvi.ucsf.edu/chimerax/download.html

**2. Login to Saga**
Log into Saga, like you have done before in the course

**3.  Setup this tutorial**
```bash
git clone https://github.com/MBV-INF4410/3D-day-2.git
cd 3D-day-2
```
**4. Unzip and inspect the LAD data**
```bash
gunzip GSE109924_lad_D0-rep1.bed.gz
```
```diff
! Look at the GSE109924_lad_D0-rep1.bed (hint: use 'head'). Do you remember what LADs were (ref. slides)?
```

**5. Inspect the LAD data in a genome browser**
Download the `GSE109924_lad_D0-rep1.bed` file to your local computer, and load the BED file in a genome browser (IGV or UCSC genome browser). 
! Are the LADs placed in expected regions in the genome (see slides from earlier today)?

**6. Run Chrom3D based on the GTrack file**
Run the following command on Saga:
```bash
Chrom3D -n 100000 -r 5.0 --nucleus -y 0.01 -l 10000 -c 0.001  chr18_bead_interactions.lads.gtrack > model.cmm
```
This step runs Chrom3D according to the model options defined in the `chr18_bead_interactions.lads.gtrack` file. 

The parameter `-n 100000` specifies the total number of iterations (100,000), `-r 5.0` sets the nuclear radius to 5μm, `--nucleus` specififies that all beads should be constrained within the nucleus, `-y 0.01` sets the volume of the chromosome model to be 1% of the total volume of the nucleus, `-l 10000` specifies that logging information should be output every 10,000 iterations. The final parameter `-c 0.001` sets the cooling-rate. When this number is set different from 0 (like here) the optimization procedure uses simulated annealing. The temperature will decrease gradually during the simulation with the slope determined by c for each accepted move. The simulated annealing approach works better/faster in single chromosome models, whereas a Metropolis-Hastings (`-c 0` [default]) criterion is better for larger systems consisting of multiple chromosomes.

**7. Color the LAD beads **
To be able to visualize the LADs, we will use a Python script to add a red color to those beads that overlap with LADs:

```bash
awk '$6==1' chr18_bead_interactions.lads.gtrack | cut -f 4 > lads.ids
python processing_scripts/color_beads.py model.cmm lads.ids 255,0,0 OVERRIDE > model_redlad.cmm
```

```diff
Using the `awk` script above, the `lads.ids` now contains the ids (4th column) of all the beads with a periphery constraint (6th column) of 1. The `color_beads.py` script specifies that all beads with ids listed in `lads.ids` should be colored in RGB value `255,0,0` (i.e. red color) in the Chrom3D output file ([CMM](https://www.cgl.ucsf.edu/chimera/docs/ContributedSoftware/volumepathtracer/volumepathtracer.html#markerfiles) file format). The `OVERRIDE` keyword specifies that any existing color definitions in the file should be ignored. 
```

**8. Visualizing `model.cmm` in ChimeraX**
- Download the `model_readlad.cmm` file to your local computer
- The resulting `model_readlad.cmm` can opened in ChimeraX and displays of these turned on and off in the bottom right "Models" panel. 
- Use the command `shape sphere center 0,0,0 radius 5.0 color #ffc9b5 slab 0.5` in the "Command" field in the bottom panel to display a nucleus structure on top of the model view. 
- Change the opacity of the nucleus model by clicking the colored square called "sphere" in the "Models" panel in the bottom right of the view, and select e.g. 30%. 
- Write "clip" in the "Command" fild to clip this nucleus into a tomographic view. The model can also be tilted to allow a better perception of depth in the structures. 
- Adjust the structure so that you display the position of the chromosome inside the nucleus and the position of the LADs.
- Click the Save icon in the top left corer to save the structure (use .jpeg)
- Upload the .jpg file from above this Padlet:  https://uio.padlet.org/jonaspaulsen/u03onwt5f8nuswbk

```diff
! Where is the chromosome located in the nucleus?
! Are the LADs placed in the expected positions?
```



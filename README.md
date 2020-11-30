# 3D-day-2
**1. Install ChimeraX

**2. Login to Saga

**3.  Setup this tutorial**
```bash
git clone XXXX
cd XXXX
```
**4. Download LAD data***

Download Lamin B1 ChIP-seq LADs from the same cells that we worked with yesterday:
```bash
mkdir lad
curl -o lad/GSE109924_lad_D0-rep1.bed.gz https://ftp.ncbi.nlm.nih.gov/geo/series/GSE109nnn/GSE109924/suppl/GSE109924_lad_D0-rep1.bed.gz
gunzip lad/GSE109924_lad_D0-rep1.bed.gz
```

```diff
! Look at the GSE109924_lad_D0-rep1.bed in a genome browser (IGV or UCSC genome browser). 
! Are the LADs placed in expected regions in the genome (see slides from earlier today)?
```

**5. Run Chrom3D based on the GTrack file**
```bash
Chrom3D -n 100000 -r 5.0 --nucleus -y 0.01 -l 10000 -c 0.001  chr18_bead_interactions.lads.gtrack > model.cmm
```
This step runs Chrom3D according to the model options defined in the `chr18_bead_interactions.lads.gtrack` file. 

The parameter `-n 100000` specifies the total number of iterations (100,000), `-r 5.0` sets the nuclear radius to 5μm, `--nucleus` specififies that all beads should be constrained within the nucleus, `-y 0.01` sets the volume of the chromosome model to be 1% of the total volume of the nucleus, `-l 10000` specifies that logging information should be output every 10,000 iterations. The final parameter `-c 0.001` sets the cooling-rate. When this number is set different from 0 (like here) the optimization procedure uses simulated annealing. The temperature will decrease gradually during the simulation with the slope determined by c for each accepted move. The simulated annealing approach works better/faster in single chromosome models, whereas a Metropolis-Hastings (`-c 0` [default]) criterion is better for larger systems consisting of multiple chromosomes.

**6. Downloading and installing the required processing scripts***

As part of the Chrom3D package, several processing scripts are available to reformat and process the data to prepare for running Chrom3D.
```bash
mkdir processing_scripts
curl -OL https://github.com/Chrom3D/preprocess_scripts/archive/v.1.2.zip
unzip -j -d processing_scripts/ v.1.2.zip preprocess_scripts-v.1.2/color_beads.py
```

**7. Color beads ***
```bash
awk '$6==1' chr18_bead_interactions.lads.gtrack | cut -f 4 > lads.ids
python processing_scripts/color_beads.py model.cmm lads.ids 255,0,0 OVERRIDE > model_redlad.cmm
```

The `lads.ids` now contains the ids (4th column) of all the beads with a periphery constraint (6th column) of 1. The `processing_scripts/color_beads.py` script specifies that all beads with ids listed in `lads.ids` should be colored in RGB value `255,0,0` (i.e. red color) in the Chrom3D output file ([CMM](https://www.cgl.ucsf.edu/chimera/docs/ContributedSoftware/volumepathtracer/volumepathtracer.html#markerfiles) file format). The `OVERRIDE` keyword specifies that any existing color definitions in the file should be ignored. 

- Do the steps from step 18., but open and visualize the `model_redlad.cmm` file instead


**8. Visualizing `model.cmm` in ChimeraX**
- If you are running this step-by-step guide on a server, download the `model.cmm` file to your local computer
- The resulting `model.cmm` (and `model_redlad.cmm` from step 19) can be opened in ChimeraX and displays of these turned on and off in the bottom right "Models" panel. To generate tomographic views of models, the command `clip` can be used in the "Command:" field in the bottom panel of ChimeraX. Background color and other graphical adjustments can be performed by clicking the "Graphics" button in the top panel.
- In ChimeraX, the command `shape sphere center 0,0,0 radius 5.0 color #ffc9b5 slab 0.5` can be used in the "Command" field in the bottom panel to display a nucleus structure on top of the model view. To change opacity of the nucleus model, click the colored square called "sphere" in the "Models" panel in the bottom right of the view, and select e.g. 30%. 
- Again, "clip" can be used to clip this to generate tomographic views. The model can also be tilted to allow a better perception of depth in the structures. Figure 4 shows some of the resulting illustrations that can be generated using ChimeraX.
- Tip: if you want white background, run the command `lighting depthCueColor black` before you select White in the Background section of the top panel
- Tip: by running the command `shape sphere center 0,0,0 radius 5.0 color #ffc9b5 mesh true`, you get a mesh/grid visualization of the nuclear boundary




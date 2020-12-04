# Generating 3D genome models from Hi-C data
In this exercise we will be:
- Installing a 3D visualization software called ChimeraX on your own computers
- Logging into the Saga HPC system to perform analyses
- Inspect data from Lamina Associated Domains (LADs)
- Generating a 3D  model of chromosome 18 in human adipose stem cells
- Visualizing the 3D model in ChimeraX
- Report the results in a Padlet

In some places, there are orange lines
```diff
! Like this
```
Here, you should stop and note down your answers to the questions asked. We will go through these in plenary later, so you can see if you understood correctly. Also note down any questions you might have.

Good luck!

**1. Install ChimeraX**
Install ChimeraX on your own computer/laptop, following these instructions: https://www.rbvi.ucsf.edu/chimerax/download.html

**2. Login to the Saga server:**

```bash
ssh saga.sigma2.no
```

**3. Set up your own interactive environment**
Like earlier in the week, we will use `srun` to allocate resources in an interactive enviroment:
```bash
srun --ntasks=1 --mem-per-cpu=4G --time=2:00:00 --account=nn9987k --pty bash -i
```

**4. Setting up today's working directory in your home directory**
```bash
cd
git clone https://github.com/MBV-INF4410/3D-day-2.git
cd 3D-day-2
```

**5. Unzip and inspect the LAD data**
```bash
gunzip GSE109924_lad_D0-rep1.bed.gz
```
```diff
! Look at the GSE109924_lad_D0-rep1.bed (hint: use 'head'). Do you remember what LADs were (ref. slides)?
```

**6. Inspect chr 18 LAD data in a genome browser**
```bash
awk '$1=="chr18"' GSE109924_lad_D0-rep1.bed > lad_chr18.bed
```
Download the `lad_chr18.bed` file to your local computer, and load the BED file the UCSC genome browser: http://genome-euro.ucsc.edu/cgi-bin/hgTracks?db=hg19 . 
```diff
! Are the LADs on chromosome 18 placed in expected regions in the genome (see slides from earlier today)?
```

**7. Install Chrom3D**
```bash
module purge
module load Boost/1.72.0-iimpi-2020a
wget https://github.com/Chrom3D/Chrom3D/archive/v1.0.2.tar.gz
tar -zxvf v1.0.2.tar.gz
cd Chrom3D-1.0.2/
make
mv Chrom3D ../
cd ..
```
```diff
! Do you understand the above UNIX/Bash commands?
```

**8. Run Chrom3D based on the GTrack file**
Run the following command on Saga:
```bash
./Chrom3D -n 100000 -r 5.0 --nucleus -y 0.01 -l 10000 -c 0.001  chr18_bead_interactions.lads.gtrack > model.cmm
```
```diff
! Look at the output in the beginning (starting with #)
! How many chromosomes are we modeling here?
! How many beads are we modeling here?
! How many interactions from Hi-C are being used?
! How many beads are contrained towards the nuclear periphery?
```

This step runs Chrom3D according to the model options defined in the `chr18_bead_interactions.lads.gtrack` file. You can see

The parameter `-n 100000` specifies the total number of iterations (100,000), `-r 5.0` sets the nuclear radius to 5μm, `--nucleus` specififies that all beads should be constrained within the nucleus, `-y 0.01` sets the volume of the chromosome model to be 1% of the total volume of the nucleus, `-l 10000` specifies that logging information should be output every 10,000 iterations. The final parameter `-c 0.001` sets the cooling-rate. When this number is set different from 0 (like here) the optimization procedure uses simulated annealing. The temperature will decrease gradually during the simulation with the slope determined by c for each accepted move. The simulated annealing approach works better/faster in single chromosome models, whereas a Metropolis-Hastings (`-c 0` [default]) criterion is better for larger systems consisting of multiple chromosomes.

**9. Color the LAD beads**
To be able to visualize the LADs, we will use a Python script to add a red color to those beads that overlap with LADs:

```bash
awk '$6==1' chr18_bead_interactions.lads.gtrack | cut -f 4 > lads.ids
python color_beads.py model.cmm lads.ids 255,0,0 OVERRIDE > model_redlad.cmm
```

Using the `awk` script above, the `lads.ids` now contains the ids (4th column) of all the beads with a periphery constraint (6th column) of 1. The `color_beads.py` script specifies that all beads with ids listed in `lads.ids` should be colored in RGB value `255,0,0` (i.e. red color) in the Chrom3D output file ([CMM](https://www.cgl.ucsf.edu/chimera/docs/ContributedSoftware/volumepathtracer/volumepathtracer.html#markerfiles) file format). The `OVERRIDE` keyword specifies that any existing color definitions in the file should be ignored. 

**10. Visualizing `model_redlad.cmm` in ChimeraX**
- Download the `model_readlad.cmm` file to your local computer
- The resulting `model_readlad.cmm` can opened in ChimeraX and displays of these turned on and off in the bottom right "Models" panel. 
- Use the command `shape sphere center 0,0,0 radius 5.0 color #ffc9b5 slab 0.5` in the "Command" field in the bottom panel to display a nucleus structure on top of the model view. 
- Change the opacity of the nucleus model by clicking the colored square called "sphere" in the "Models" panel in the bottom right of the view, and select e.g. 30%. 
- Rotate the structure so that the chromosome model points away from you, and write `clip` in the "Command" fild to clip this nucleus into a tomographic view. The model can also be tilted to allow a better perception of depth in the structures.  (You can undo the clipping by typing `~clip`)
- Adjust the structure so that you display the position of the chromosome inside the nucleus and the position of the LADs.
- (optional): Play with the light settings (Simple, Soft, Full) and some of the other visualization settings.
- Click the Save icon in the top left corer to save the structure (use .jpeg)
- Upload the .jpg file from above this Padlet:  https://uio.padlet.org/jonaspaulsen/u03onwt5f8nuswbk

```diff
! Where is the chromosome located in the nucleus?
! Are the LADs placed in the expected positions?
```



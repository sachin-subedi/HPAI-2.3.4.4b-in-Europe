# HPAI-2.3.4.4b-in-Europe
Highly Pathogenic Avian Influenza 2.3.4.4b in Europe

## Project Objective:

- To examine Highly Pathogenic Avian Influenza spillover to domestic animals (poultry and domestic mammals -swine, cow, horse, dog, cat and others) and wild mammals (bear, whales, seals, seals etc) in Europe.
  
## Steps

### Preparation and Cleaning
- Five thousand eight hundred and ninety complete sequences of the HA genes from Influenza Virus A(H5Nx) identified in all species in Europe between January 1, 2016, and May 7, 2024, were retrieved from the GISAID genome database.
### Host categorization and species grouping:
- Different bird and species were categorized as Wild and domestic animals. We had  included sequence data that were collected from human, environment and laboratory.

| Host_species | Host_name | Host_category|
|--------------|-----------------|--------|
| Mute Swan  | Swan        |Wild Bird    |
| Red Fox    | Fox         |Wild Mammal    |
| Chicken    | Poultry      |Domestic Bird   |
| Domestic Cat   | Cat      |Domestic Mammal   |

### Seqequence Alignment
- Sequences of each genome segment were aligned using MAFFT in Seqotron
  
### Maximum Likelihood Tree
The phylogenetic signals within a given dataset was studied using the maximum-likelihood method in IQ-tree. The outlier was removed.

### TreeTime
TreeTime provides routines for ancestral sequence reconstruction and inference of molecular-clock phylogenies, i.e., a tree where all branches are scaled such that the positions of terminal nodes correspond to their sampling times and internal nodes are placed at the most likely time of divergence.

```bash
#!/bin/bash
#SBATCH --job-name=TreeTime
#SBATCH --partition=batch
#SBATCH --ntasks=1
#SBATCH --ntasks-per-node=2
#SBATCH --cpus-per-task=8  
#SBATCH --mem=120G
#SBATCH --time=7-00:00:00
#SBATCH --output=log.%j.out
#SBATCH --error=log.%j.err
#SBATCH --mail-user=ss11645@uga.edu  
#SBATCH --mail-type=END,FAIL

# Change to the directory where the job was submitted
cd $SLURM_SUBMIT_DIR

ml load Python/3.10.4-GCCcore-11.3.0

# Create a virtual environment
python -m venv treetime_env

# Activate the virtual environment
source treetime_env/bin/activate

# Install TreeTime
pip install phylo-treetime

# Create output directory
output_dir="lss"
mkdir -p $output_dir

# treetime --sequence-length 1712 --tree tree.nwk --dates metadata.csv --clock-filter 3.0 --reroot ML --outdir $output_dir

# treetime --sequence-length 1712 --tree tree.nwk --dates metadata.csv --clock-filter 3.0 --reroot best --outdir $output_dir

treetime --sequence-length 1712 --tree tree.nwk --dates metadata.csv --clock-filter 3.0 --reroot least-squares --outdir $output_dir

# Deactivate the virtual environment
deactivate

```


```bash
#!/bin/bash

#SBATCH --job-name=IQ_tree
#SBATCH --partition=bahl_p
#SBATCH --nodes=2
#SBATCH --ntasks=16
#SBATCH --ntasks-per-node=8
#SBATCH --cpus-per-task=1    
#SBATCH --mem=120G
#SBATCH --cpus-per-task=4
#SBATCH --time=7-00:00:00
#SBATCH --output=%x_%j.out
#SBATCH --error=%x_%j.err
#SBATCH --mail-user=ss11645@uga.edu
#SBATCH --mail-type=END,FAIL    #Mail events (NONE, BEGIN, END, FAIL, ALL)

cd $SLURM_SUBMIT_DIR

ml load IQ-TREE/2.2.2.6-gompi-2022a
iqtree2 -s H5_Aligned_EU2.fasta -m GTR+I+G

# ml load  IQ-TREE/1.6.12-foss-2022a
# iqtree -s H5_AL1.fasta -m MFP
# iqtree -s H5_Aligned_EU2.fasta -m GTR+I+G
```

### Subsampling
- Using Nextstrain

### Setting BEAUTi
- Dates were parsed.
- Two traits were created namely, Host and Regions.
- GTR gamma model was selected for default values and Unsymmetric substitution model for all other traits. Infer scocial networls for BSSVS was selected.
- Uncorrelated relaxed clock and lognormal distribution with continuous quantile parameterization was done with default data. Strict clock was selected for all other traits.
- For tree models, Coalescent: GMRF Bayesian Skyride was selected.
- Ancestral states and state change counts were reconstructed.
- For priors with initial = 0.0033 with uniform distribution from 0 to 1.
- MCMC Chain length of 100000000 was selected and xml file was generated for beast run.

### BEAST run (non-GPU)
```bash
#!/bin/bash
#SBATCH --job-name=beast_EU
#SBATCH --partition=bahl_p
#SBATCH --nodes=4
#SBATCH --ntasks=16
#SBATCH --ntasks-per-node=16
#SBATCH --cpus-per-task=2   
#SBATCH --mem=120G
#SBATCH --time=7-00:00:00
#SBATCH --output=log.%j.out
#SBATCH --error=log.%j.err
#SBATCH --mail-user=xx12345@uga.edu  
#SBATCH --mail-type=END,FAIL

cd $SLURM_SUBMIT_DIR

ml load Beast/1.10.4-GCC-11.3.0
beast -threads 4 -beagle -beagle_SSE -overwrite xx.xml
```


### BEAST run (GPU)
```bash
#!/bin/bash
#SBATCH --job-name=beast_GPU
#SBATCH --partition=gpu_p
#SBATCH --gres=gpu:A100:1
#SBATCH --nodes=4
#SBATCH --ntasks=1
#SBATCH --ntasks-per-node=2
#SBATCH --cpus-per-task=8  
#SBATCH --mem=200G
#SBATCH --time=7-00:00:00
#SBATCH --output=log.%j.out
#SBATCH --error=log.%j.err
#SBATCH --mail-user=xx12345@uga.edu  
#SBATCH --mail-type=END,FAIL

cd $SLURM_SUBMIT_DIR

ml load Beast/1.10.4-GCC-11.3.0-CUDA-11.4.1
beast -beagle -beagle_SSE -overwrite xx.xml
```

### Markov Jumps and Rewards
```bash
		<!-- START Ancestral state reconstruction                                    -->
		<parameter id="Host.count" value=" 
0.0 1.0 1.0 1.0 1.0 1.0 1.0 1.0 1.0
1.0 0.0 1.0 1.0 1.0 1.0 1.0 1.0 1.0
1.0 1.0 0.0 1.0 1.0 1.0 1.0 1.0 1.0
1.0 1.0 1.0 0.0 1.0 1.0 1.0 1.0 1.0
1.0 1.0 1.0 1.0 0.0 1.0 1.0 1.0 1.0
1.0 1.0 1.0 1.0 1.0 0.0 1.0 1.0 1.0
1.0 1.0 1.0 1.0 1.0 1.0 0.0 1.0 1.0
1.0 1.0 1.0 1.0 1.0 1.0 1.0 0.0 1.0
1.0 1.0 1.0 1.0 1.0 1.0 1.0 1.0 0.0"/>


		<rewards>
			<parameter id="DomesticBird_reward"  value="1.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0" />
			<parameter id="DomesticMammal_reward"  value="0.0 1.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0" />
			<parameter id="Environment_reward"  value="0.0 0.0 1.0 0.0 0.0 0.0 0.0 0.0 0.0" />
			<parameter id="Feces_reward"  value="0.0 0.0 0.0 1.0 0.0 0.0 0.0 0.0 0.0" />
			<parameter id="Human_reward"  value="0.0 0.0 0.0 0.0 1.0 0.0 0.0 0.0 0.0" />
			<parameter id="OtherBird_reward"  value="0.0 0.0 0.0 0.0 0.0 1.0 0.0 0.0 0.0" />
			<parameter id="Unknown_reward"  value="0.0 0.0 0.0 0.0 0.0 0.0 1.0 0.0 0.0" />
			<parameter id="WildBird_reward"  value="0.0 1.0 0.0 0.0 0.0 0.0 0.0 1.0 0.0" />
			<parameter id="WildMammal_reward"  value="0.0 1.0 0.0 0.0 0.0 0.0 0.0 0.0 1.0" />
		</rewards>
			
		<!-- END Ancestral state reconstruction                                      -->

	</markovJumpsTreeLikelihood>

		<!-- END Ancestral state reconstruction                                      -->


		<!-- START Ancestral state reconstruction                                    -->
		<parameter id="Region.count" value=" 
0.0 1.0 1.0 1.0 1.0 1.0
1.0 0.0 1.0 1.0 1.0 1.0
1.0 1.0 0.0 1.0 1.0 1.0
1.0 1.0 1.0 0.0 1.0 1.0
1.0 1.0 1.0 1.0 0.0 1.0
1.0 1.0 1.0 1.0 1.0 0.0 "/>


		<rewards>
			<parameter id="CentralEurope_reward"  value="1.0 0.0 0.0 0.0 0.0 0.0" />
			<parameter id="EasternEurope_reward"  value="0.0 1.0 0.0 0.0 0.0 0.0" />
			<parameter id="NorthernEurope_reward"  value="0.0 0.0 1.0 0.0 0.0 0.0" />
			<parameter id="Others_reward"  value="0.0 0.0 0.0 1.0 0.0 0.0" />
			<parameter id="SouthernEurope_reward"  value="0.0 0.0 0.0 0.0 1.0 0.0" />
			<parameter id="WesternEurope_reward"  value="0.0 0.0 0.0 0.0 0.0 1.0" />
		</rewards>
		<!-- END Ancestral state reconstruction                                      -->

	</markovJumpsTreeLikelihood>

		<!-- END Ancestral state reconstruction                                      -->
		
		
<!--
	</ancestralTreeLikelihood>
-->
```
### LogCombiner and TreeAnnotator
- LogCombiner allows you to combine log and tree files from multiple independent runs of BEAST.
- TreeAnnotator is a program to summarize the information from a sample of trees produced by BEAST onto a single “target” tree.

``` bash
#!/bin/bash
#SBATCH --job-name=LCTA
#SBATCH --partition=batch
#SBATCH --ntasks=1
#SBATCH --ntasks-per-node=2
#SBATCH --cpus-per-task=8  
#SBATCH --mem=120G
#SBATCH --time=7-00:00:00
#SBATCH --output=log.%j.out
#SBATCH --error=log.%j.err
#SBATCH --mail-user=ss11645@uga.edu  
#SBATCH --mail-type=END,FAIL

# Change to the directory where the job was submitted
cd $SLURM_SUBMIT_DIR

# Load the BEAST module
ml load Beast/1.10.4-GCC-11.3.0

# Run LogCombiner with the specified options
logcombiner -trees -resample 10000 subsampled_23May.trees 1.trees

# Check if LogCombiner succeeded before running TreeAnnotator
if [ $? -eq 0 ]; then
    echo "LogCombiner completed successfully, proceeding to TreeAnnotator."

    # Run TreeAnnotator with the specified options
    treeannotator -limit 0.95 -heights median 1.trees 11.tree

    if [ $? -eq 0 ]; then
        echo "TreeAnnotator completed successfully."
    else
        echo "TreeAnnotator encountered an error." >&2
    fi
else
    echo "LogCombiner encountered an error, aborting TreeAnnotator." >&2
fi
```

### SpreaD3: Spatial Phylogenetic Reconstruction of Evolutionary Dynamics using Data-Driven Documents
- SpreaD3 is a application to analyze and visualize pathogen phylodynamic reconstructions resulting from Bayesian inference of sequence and trait evolutionary processes.

```bash
#!/bin/bash
#SBATCH --job-name=SpreaD3
#SBATCH --partition=bahl_p
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=2
#SBATCH --mem=16G
#SBATCH --time=7-00:00:00
#SBATCH --output=log.%j.out
#SBATCH --error=log.%j.err
#SBATCH --mail-user=ss11645@uga.edu  
#SBATCH --mail-type=END,FAIL

# Change to the directory where the job was submitted
cd $SLURM_SUBMIT_DIR

# Load the Java module
ml load Java/17.0.6

# Define the input log file
INPUT_LOG=subsampled_23May.Region.rates.log

# Run SpreaD3 using the java -jar command directly
java -jar /home/ss11645/H5_EU/Beast/MarkovRewards/Run1/spreaD3_v0.9.6.jar -parse -locations /home/ss11645/H5_EU/Beast/MarkovRewards/Run1/locations.txt -geojson /home/ss11645/H5_EU/Beast/MarkovRewards/Run1/europe.geojson -log $INPUT_LOG -burnin 10 -output /home/ss11645/H5_EU/Beast/MarkovRewards/Run1/out1.json

# Render D3 output
java -jar /home/ss11645/H5_EU/Beast/MarkovRewards/Run1/spreaD3_v0.9.6.jar -render d3 -json /home/ss11645/H5_EU/Beast/MarkovRewards/Run1/out1.json -output /home/ss11645/H5_EU/Beast/MarkovRewards/Run1/rendered

# Render KML output
java -jar /home/ss11645/H5_EU/Beast/MarkovRewards/Run1/spreaD3_v0.9.6.jar -render kml -json /home/ss11645/H5_EU/Beast/MarkovRewards/Run1/out1.json -output /home/ss11645/H5_EU/Beast/MarkovRewards/Run1/rendered.kml
```









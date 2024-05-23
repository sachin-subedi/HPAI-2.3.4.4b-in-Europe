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

```bash
treetime --sequence-length 1712 --tree /Users/sachinsubedi/Desktop/Treetime/may22/tree.nwk --dates /Users/sachinsubedi/Desktop/Treetime/may22/metadata1.csv --clock-filter 3.0 --reroot ML
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
### Setting BEAUTi
#### Run1:
- Dates were parsed.
- Four traits were created namely, Host_Category, Host_Names, Subtype and Country.
- GTR gamma model was selected for default values and Symmetric substitution model for all other traits.
- Uncorrelated relaxed clock and lognormal distribution with continuous quantile parameterization was done with default data. Strict clock was selected for all other traits.
- For tree models, Coalescent: GMRF Bayesian Skyride was selected.
- Ancestral states and state change counts were reconstructed.
- For priors with initial = 0.0033 with uniform distribution from 0 to 1.
- MCMC Chain length of 100000000 was selected and xml file was generated for beast run.

#### Run2:
- Dates were parsed.
- Four traits were created namely, Host_Category, Host_Names, Subtype and Country.
- GTR gamma model was selected for default values and Unsymmetric substitution model for all other traits. Infer scocial networls for BSSVS was selected.
- Uncorrelated relaxed clock and lognormal distribution with continuous quantile parameterization was done with default data. Strict clock was selected for all other traits.
- For tree models, Coalescent Constant Size was selected.
- Ancestral states and state change counts were reconstructed.
- For priors with initial = 0.0033 with uniform distribution from 0 to 1.
- MCMC Chain length of 100000000 was selected and xml file was generated for beast run.

#### Run3:
- Dates were parsed.
- Four traits were created namely, Host_Category, Host_Names, Subtype and Country.
- GTR gamma model was selected for default values and Unsymmetric substitution model for all other traits. Infer scocial networls for BSSVS was selected.
- Uncorrelated relaxed clock and lognormal distribution with continuous quantile parameterization was done with default data. Strict clock was selected for all other traits.
- For tree models, Coalescent: GMRF Bayesian Skyride was selected.
- Ancestral states and state change counts were reconstructed.
- For priors with initial = 0.0033 with uniform distribution from 0 to 1.
- MCMC Chain length of 100000000 was selected and xml file was generated for beast run.

### Run4
- Dates were parsed.
- Four traits were created namely, Host_Category, Host_Names, Subtype and Country.
- HKY estimated model was selected for default values and Unsymmetric substitution model for all other traits. Infer scocial networls for BSSVS was selected.
- Uncorrelated relaxed clock and lognormal distribution with continuous quantile parameterization was done with default data. Strict clock was selected for all other traits.
- For tree models, Coalescent: GMRF Bayesian Skyride was selected.
- Ancestral states and state change counts were reconstructed.
- For priors with initial = 0.0033 with lognormal distribution with mean 0.0033, standard deviation 0.01 and offset 0.0.
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







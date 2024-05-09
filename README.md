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
The phylogenetic signals within a given dataset was studied using the maximum-likelihood method in IQ-tree.

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


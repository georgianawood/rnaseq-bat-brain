# Title in Progress #
Author: Georgiana N. Wood
Last Update 11/17/22

**Objective:** XYZ

***Reference tutorial used: https://github.com/CBC-UCONN/RNAseq_nonmodel***

1. Retrieve brain transcriptomes from NCBI
* SRR2153218 (Megaderma lyra)
* SRR2153219 (Rhinopoma hardwickii)
* SRR2153221 (Tadarida teniotis)
* SRR2153222 (Murina leucogaster)
* SRR2153223 (Scotophilus kuhlii)
* SRR2153224 (Myotis ricketti)

After creating a text file containing each name of my desired sequences, the following code was used to download a total of six transcriptomes.

```cat  SRAAccessions.txt | parallel fastq-dump --split-files {}```

Once the SRA data has completed its download, activate the custom environment given to you by your wonderful mentor Lexi.

```module load anaconda/3 ```
```source activate /gpfs/projects/DavalosGroup/Lexi/envs/genomics```

2. Assesing read quality for each brain transcriptome

Now that all of the sequences have been downloaded, the next step is to generate reports for the fastq files using ```FastQC```. The following code was obtained from the reference repository, and altered for my project.

```fastqc --outdir fastqc rawfastq/SRR2153219_1.fastq rawfastq/SRR2153219_2.fastq```

Alternatively, to make things more efficient you can simply run a slurm script written by the aforementioned amazing mentor. 

*The script is shown below for reference, but all scripts used are located in /gpfs/projects/DavalosGroup/Georgie/rnaseq/scripts*

```#!/bin/bash
#SBATCH --ntasks-per-node=24
#SBATCH --time=48:00:00
#SBATCH --nodes=2
#SBATCH --job-name=fastqc
#SBATCH --output=/gpfs/projects/DavalosGroup/Georgie/rnaseq/%j.out.txt
#SBATCH --error=/gpfs/projects/DavalosGroup/Georgie/rnaseq/%j.err.txt
#SBATCH -p long-24core
#SBATCH --mail-user=georgiana.wood@stonybrook.edu
#SBATCH --mail-type=begin
#SBATCH --mail-type=end

module load anaconda/3
source activate activate /gpfs/projects/DavalosGroup/Lexi/envs/genomics

INFILE=$1
cat /gpfs/projects/DavalosGroup/Georgie/rnaseq/$INFILE | while read line; do
Read1=/gpfs/projects/DavalosGroup/Georgie/rnaseq/rawfastq/${line}_1.fastq;
Read2=/gpfs/projects/DavalosGroup/Georgie/rnaseq/rawfastq/${line}_2.fastq;

fastqc --outdir /gpfs/projects/DavalosGroup/Georgie/rnaseq/fastqc $Read1 $Read2;

done
```

All quality reports look great, and are located in the fastqc folder.

3. Removing adapters

Using fastp, all .fastq files are trimmed of their adapters. The script is */gpfs/projects/DavalosGroup/Georgie/rnaseq/scripts.fastploop.sh*

4. *De Novo* Assembly Using Trinity
















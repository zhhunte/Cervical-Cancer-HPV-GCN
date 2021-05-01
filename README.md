# Cervical-Cancer-HPV-GCN
This workflow attempted to analyze SRA data related to cervical organoids infected with human papillomavirus through GEM construction, preprocessing, and subsequent Gene Co-expression Network analysis. (Keyword is attempt) 
## Lab Overview
Human Papillomavirus (HPV) is the most commonly sexually transmitted infection in the United States and endemic worldwide. However, even more troubling is the instigation of the virus in many different forms of cancer. In 2017, it was reported that HPV-related cancers were globally responsible for 8.6% of all cancer cases per year in women and 0.8% of all cases per year in men. One of the most prolific types of HPV-related cancers is observed in the cervix, making up the vast majority of incidenct cancer cases (https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5520228/). A Gene Co-expression Network (GCN) of cervical cancer tissue with a HPV infection could reveal malfunctioning genes whose incorrect expression may be due to the viral infection and allow for tumor development. 
## Lab Objectives
To conduct this analysis, 
* Create a GEM using SRA data and GEMmaker.
* Preprocess the GEM through log2 and quantile normalization.
* Construct a GCN from the processed GEM.
## Lab Tasks
### Task A: Making the GEM
1. *Clone GEMmaker*. 
Make a new working directory and use github to clone GEMmaker inside that directory. 
> git clone https://github.com/SystemsGenetics/GEMmaker.git --branch develop
2. *Download the human genome*.
Go the GEMmaker/demo/references directory. The CORG genome has to be replaced with the human to construct the GEM. 
> wget http://ftp.ensembl.org/pub/release-101/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz
> wget ftp://ftp.ensembl.org/pub/release-101/gtf/homo_sapiens/Homo_sapiens.GRCh38.101.gtf.gz
Use the gunzip command on both of the resulting files. 
3. *Index the human genome*.
The FASTA human genome file has to be indexed so FASTQ files can be aligned to it. Using singularity, "pull" a hisat2 container and index the reference genome.
> singularity exec -B /scratch/GEMmaker/GEMmaker/demo/references/ docker://gemmaker/hisat2:2.1.0-1.1 hisat2-build Homo_sapiens.GRCh38.dna.primary_assembly.fa HG38
The output will be a series of files that are prefixed with the ‘HG38’ label.
4. *Change the list of SRA RNAseq runs to be input into GEMmaker*.
Delete the 3 existing .fastq files in the /GEMmaker/demo directory. Replace the contents of the SRA_IDs.txt file in the /GEMmaker/demo directory to contain these eight SRA IDs:
SRR13858066
SRR13858067
SRR13858068
SRR13858069
SRR13858070
SRR13858071
SRR13858072
SRR13858073
5. *Edit the nextflow.config file in the /GEMmaker directory*.
* Change local_sample_files from “_{1,2}.fastq” to “none”.
* Scroll down to the quantification section. Change "method" to "= 'hisat2'".
* Scroll down to the hisat2 section. Change "base_name" to "HG38".
* Change "index_dir" to "./demo/references/HG38.indexed" or whatever path points towards to the directory containing the indexed genome files. For example, I made a directory called "HG38.indexed" within the /references directory. 
* Change "gtf_file" to "./demo/references/Homo_sapiens.GRCh38.101.gtf" or whatever path points towards the directory containing the .gtf file and its name. 
* Save the changes and cat the file to make sure everything looks correct. 
6. *Run GEMmaker.*
Using nextflow, run GEMmaker from /GEMmaker directory containing the main.nf file. 
> nextflow run main.nf -profile standard -with-singularity -with-report -with-timeline
### Task B: Preprocessing the GEM
1. *Install the GEMprep environment*.
Create and activate a conda environment called gemprep containing all of the necessary dependencies. 
> conda create -n gemprep python=3.6 matplotlib mpi4py numpy pandas r scikit-learn seaborn
source 

> conda activate gemprep

2. *Clone GEMprep*.
Use github to clone GEMprep in the working directory. 
> git clone https://github.com/SystemsGenetics/GEMprep
Copy the cervix-HPV GEM into the resulting /GEMprep directory and 'cd' there. 
3. *Normalize the GEM*.
* Log2 transformation:
> python ./bin/normalize.py cervix-HPV.GEM.txt  cervix-HPV.GEM.log2.txt --log2
* Quantile normalization:
> python ./bin/normalize.py cervix-HPV.GEM.txt  cervix-HPV.GEM.log2.quantile.txt --quantile
### Task C: Constructing the GCN
1. *Add '.emx.txt' to the end of the input GEM file*.
Rename the input GEM file so that it ends in '.emx.txt'. This is so KINC recognizes it as an input GEM. For example, if the GEM file is named 'cervix-HPV.GEM.log2.quantile.txt', it should be renamed as 'cervix-HPV.GEM.log2.quantile.emx.txt'.
2. *Run the Nextflow version of the KINC workflow*. 
First, make sure there is a /tmpfiles directory for Nextflow to write files. For the purposes of this lab, /tmpfiles was within the directory '/home/student'.
> export TMPDIR=/home/student/tmpfiles
Make a new directory called '/input' within the working directory and copy the '.emx.txt' GEM file into it. This is where KINC looks for the input GEM. Once in, the workflow should be ready to run from the working directory: 
> nextflow run systemsgenetics/KINC-nf -with-singularity -resume
**WARNING: I did not succeed in GCN construction due to a 'threshold.rmt' error while running the KINC workflow.**
3. *Visualize the GCN*. 
Install Cytoscape on your local computer.  Follow install instructions here: https://cytoscape.org/. Open Cytoscape and drag in the GCN network file to visualize.

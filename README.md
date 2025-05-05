# nextflow_snp_calling_linux
Nextflow pipeline for Linux systems without job schedulers



This pipeline takes paired-end fastq reads, a reference genome and a gff file and will produce:
- a minimally filtered vcf (removing SNPs where all indidivuals are homozyogous ALT and any SNP with MQ < 30).
- 3 depth statistics files per dataset: samples genes depth, samples windows depth and samples whole-genome depth.

To run the workflow:
<pre>nextflow run main.nf -config nextflow.config --ref_genome /path/to/reference_genome.fasta --gff_file /path/to/genes.gff</pre>


# Options

--reads (default CWD: ./*{1,2}.fastq.gz) ### this can be changed, use it to match your raw fastq reads path and names patterns (ie. fq.gz)

--outdir (default: ./output/) ### this can be changed to any directory

--ref_genome (No default, give full path)

--gff_file (No default, give full path)


# Important


It is required to pull all the apptainer images as sif files and link them to the directory where you save them in the config file nextflow.config

Apptainer images vailable at: [https://github.com/RepAdapt/singularity/blob/main/RepAdaptSingularity.md](https://github.com/RepAdapt/singularity/blob/main/RepAdaptSingularity.imagelocations.md)


This pipeline assumes that each sample has a single pair of paired end reads.


# Comments

- **Reference too fragmented -- stitching the reference genome:**  
  If a reference genome is highly fragmented, consisting of thousands or even millions of scaffolds, it is beneficial to stitch them into larger contiguous sequences before running the SNP calling pipeline to reduce the total number of scaffolds.  
  Having a reference composed of too many scaffolds will cause errors in the indel realignment step with GATK3 – I am not sure which threshold is “too many". This issue mite actually be caused by having very short scaffolds rather than the number of scaffolds.  
  Additionally, the pipeline parallelizes the SNP calling step (`bcftools mpileup + call`) by chromosome (calling SNPs in each chromosome in parallel), therefore having a very fragmented reference would result in sending thousands (or millions) of very fast jobs – it would still work but it would be an overkill and probably not ideal for queue times on a job scheduler.  
  So, if your reference is too fragmented, please stitch it and unstitch it after SNP calling!  

- **Reference must have `.fasta` suffix while genes GFF must have `.gff` suffix.**  

- **Make sure that the `.gff` file and reference genome use the same exact chromosome names.**  
  If this is not the case the depth of coverage statistics will not be calculated. The names need to be exactly the same, so if, for example, the reference has `chromosome_1` and the GFF has `chr_1`, these will have to be changed to the same naming.  

- **Make sure the chromosome/scaffold names do not contain weird characters that may break commands.**  
  A very unusual case that I found was a reference that had `|` pipes included in the chromosome names – this can cause a lot of issues, as the pipe `|` may be interpreted as a Linux pipe command.  

- **If a process fails, the first thing to check is whether it was due to low run time or RAM.**  
  RAM and run time can be easily edited for each process by modifying its corresponding script in the `modules` directory. I tried to provide high enough values that will work for most datasets, but if your dataset is particularly large (in terms of reference size or raw FASTQ files size per sample), it might be necessary to increase RAM and run time for some processes in the modules directory.  




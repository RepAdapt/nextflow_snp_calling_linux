# nextflow_snp_calling_linux
Nextflow pipeline for Linux systems without job schedulers



This pipeline takes fastq reads, a reference genome and a gff file and will produce:
- a minimally filtered vcf (removing SNPs where all indidivuals are homozyogous ALT and any SNP with MQ < 30).
- 3 depth statistics files per dataset: samples genes depth, samples windows depth and samples whole-genome depth.

To run the workflow:
<pre>nextflow run main.nf -config nextflow.config --ref_genome /path/to/reference_genome.fasta --gff_file /path/to/genes.gff</pre>


<b>Available options:</b>

--reads (default CWD: ./*{1,2}.fastq.gz) ### this can be changed, use it to match your raw fastq reads path and names patterns (ie. fq.gz)

--outdir (default: ./output/) ### this can be changed to any directory

--ref_genome (No default, give full path)

--gff_file (No default, give full path)


<b>IMPORTANT</b>

The reference genome file MUST HAVE .fasta suffix (change it to .fasta if yours is .fa)

The GFF file MUST HAVE .gff suffix

It is required to pull all the apptainer images as sif files and link them to the directory where you save them in the config file nextflow.config

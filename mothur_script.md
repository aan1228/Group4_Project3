# Methods

## Housekeeping

General workflow (Figure 1) and directory structure (Figure 2) are below.

![Workflow](https://i.imgur.com/TmsmL5U.png)
Figure 1. Workflow Structure.

![Directory](https://i.imgur.com/RX4KmGu.png)
Figure 2. Directory Structure.

FASTQC was run on the paired FASTA files at 165 m depth to determine sequence quality. 
```
cd /home/micb405/Group4/project3
fastqc -t 2 /home/micb405/data/project_3/SI072_S3_165_1.fastq /home/micb405/data/project_3/SI072_S3_165_2.fastq -o FASTQC
```
The following commands are done in ```mothur```. They could also have been run in bash by typing ```mothur "#command"```.

## Assembly

Set the output directory to ```project3/mothur_output```. This means that all outputs are saved into this directory.
```
set.dir(output=/home/micb405/Group4/project3/output)
```
Make a file called ```depth165.files```. It will contain all paired FASTA files of all depths, so use a text editor to remove depths that are not 165 metres. These 2x300 paired end reads were combined into contigs, generating a fasta file called ```depth165.trim.contigs.fasta```. This fasta file was summarized in ```depth165.trim.contigs.summary``` to see what the range of contig lengths were.  
```
make.file(inputdir=/home/micb405/data/project_3, prefix=depth165)
make.contigs(file=depth165.files)
summary.seqs(fasta=depth165.trim.contigs.fasta)
```
The outputs to these commands were later placed in the directory called ```contigs```.

## Filter 1
The first filtering step removed sequences with lengths less than 295 or greater than 315, or with ambiguous bases greater than 5 or homopolymers greater than 10. This generated another fasta file called ```depth165.trim.contigs.good.fasta```.
```
screen.seqs(fasta=depth165.trim.contigs.fasta, group=depth165.contigs.groups, maxambig=5, maxhomop=10, minlength=203, maxlength=313)
```
Then we determined the number of unique sequences present as well as reduce the amount of processing required for downstream steps. A count table was generated to merge the .names and .groups files together for downstream steps, called ```depth165.trim.contigs.good.count_table```. The number of contigs that passed quality control were summarized in ```depth165.trim.contigs.good.unique.summary```.
```
unique.seqs(fasta=depth165.trim.contigs.good.fasta)
count.seqs(name=depth165.trim.contigs.good.names, group=depth165.contigs.good.groups)
summary.seqs(fasta=depth165.trim.contigs.good.unique.fasta, count=depth165.trim.contigs.good.count_table)
```

## Filter 2

Alignment was done against the SILVA database of 16S rRNA. ```flip=T``` was chosen to allow mothur to try aligning with the reverse complement. Though this increases the computation required, it would also increase the chances that alignments can be made for more of the contigs. A fasta file was generated called ```depth165.trim.contigs.good.unique.align```. The results were then summarized.
```
align.seqs(fasta=depth165.trim.contigs.good.unique.fasta, reference=/home/micb405/data/project_3/databases/silva.nr_v128.align, flip=T)
summary.seqs(fasta=depth165.trim.contigs.good.unique.align, count=depth165.trim.contigs.good.count_table)
```

The second filtering step removed sequences starting after 10370 or ending before 22539 based on alignment position, outputting a fasta file called ```depth165.trim.contigs.good.unique.good.align``` and its corresponding count table. Results were summarized in ```depth165.trim.contigs.good.unique.good.summary```.

```
screen.seqs(fasta=depth165.trim.contigs.good.unique.align, count=depth165.trim.contigs.good.count_table, summary=depth165.trim.contigs.good.unique.summary, start=10370, end=22539)
summary.seqs(fasta=depth165.trim.contigs.good.unique.good.align, count=depth165.trim.contigs.good.good.count_table)

```
Since the alignment earlier added . and - to the fasta file, these were filtered out to generate a fasta file called ```depth165.trim.contigs.good.unique.good.filter.fasta``` and corresponding count table. This step may have also resulted in duplicates, so the number of unique sequences were determined again and outputted in a fasta file called ```depth165.trim.contigs.good.unique.good.filter.unique.fasta``` and corresponding count table. 
```
filter.seqs(fasta=depth165.trim.contigs.good.unique.good.align, vertical=T, trump=.)
unique.seqs(fasta=depth165.trim.contigs.good.unique.good.filter.fasta, count=depth165.trim.contigs.good.good.count_table)
```
The outputs of the two filtering steps were later put into a directory called ```screen```.

## Preclustering
Since 2x300 MiSeq sequencing and PCR amplification introduces around 2-4 errors per 300 bp, diff=4 was chosen for pre-clustering, outputting a fasta file called ```depth165.trim.contigs.good.unique.good.filter.unique.precluster.fasta``` and its corresponding count table. Results were summarized and generated a summary file called ```depth165.trim.contigs.good.unique.good.filter.unique.precluster.summary```.
```
pre.cluster(fasta=depth165.trim.contigs.good.unique.good.filter.unique.fasta, count=depth165.trim.contigs.good.unique.good.filter.count_table, diffs=4)
summary.seqs(fasta=depth165.trim.contigs.good.unique.good.filter.unique.precluster.fasta, count=depth165.trim.contigs.good.unique.good.filter.unique.precluster.count_table)
```
The outputs of the preclustering step was later put into a directory called ```precluster```.

## Chimera Removal
chimera.uchime identified chimeric sequences, which were then removed from the fasta file generated after preclustering according to the outputted .accnos file. This outputted a fasta file called ```depth165.trim.contigs.good.unique.good.filter.unique.precluster.pick.fasta``` and its corresponding count table. The results were then summarized in ```depth165.trim.contigs.good.unique.good.filter.unique.precluster.pick.summary```.
```
chimera.uchime(fasta=depth165.trim.contigs.good.unique.good.filter.unique.precluster.fasta, count=depth165.trim.contigs.good.unique.good.filter.unique.precluster.count_table, dereplicate=t)
remove.seqs(fasta=depth165.trim.contigs.good.unique.good.filter.unique.precluster.fasta, count=depth165.trim.contigs.good.unique.good.filter.unique.precluster.denovo.uchime.pick.count_table, accnos=depth165.trim.contigs.good.unique.good.filter.unique.precluster.denovo.uchime.accnos)
summary.seqs(fasta=depth165.trim.contigs.good.unique.good.filter.unique.precluster.pick.fasta, count=depth165.trim.contigs.good.unique.good.filter.unique.precluster.denovo.uchime.pick.pick.count_table)
```
## Singleton Removal
Singletons were identified, resulting in a .abund.fasta and .rare.fasta file. The .abund file contained sequences that were not singletons, and thus was used for downstream processes. The results were summarized.
```
split.abund(fasta=depth165.trim.contigs.good.unique.good.filter.unique.precluster.pick.fasta, count=depth165.trim.contigs.good.unique.good.filter.unique.precluster.denovo.uchime.pick.pick.count_table, cutoff=1)
summary.seqs(fasta=depth165.trim.contigs.good.unique.good.filter.unique.precluster.pick.abund.fasta, count=depth165.trim.contigs.good.unique.good.filter.unique.precluster.denovo.uchime.pick.pick.abund.count_table)
```
The outputs of chimera and singleton removal were later put into a directory called ```chimera```.

## Clustering and Annotation
The ```depth165.trim.contigs.good.unique.good.filter.unique.precluster.pick.abund.fasta``` file generated after singleton removal was renamed to ```depth165.final.fasta``` avoid confusion, and its corresponding count table was renamed to ```depth165.final.count_table```. A distance matrix was constructed with this file, called ```depth165.final.phylip.dist```. output=lt was chosen to make a phylip formatted lower triangle matrix rather than a column-formatted matrix to save space.
```
system(cp depth165.trim.contigs.good.unique.good.filter.unique.precluster.pick.abund.fasta depth165.final.fasta)
system(cp depth165.trim.contigs.good.unique.good.filter.unique.precluster.denovo.uchime.pick.pick.abund.count_table depth165.final.count_table)
dist.seqs(fasta=depth165.final.fasta, output=lt)
```
Clustering was done using opti. A level of 97% similarity (label=0.03) was chosen to make OTUs at the species level. Clustering resulted in a variety of outputs, the most important ones being the .list file which maps the number of sequences to each OTU clustered. This was combined into a human readable OTU called ```depth165.final.phylip.opti_mcc.shared```.
```
cluster(phylip=depth165.final.phylip.dist, count=depth165.final.count_table, method=opti)"
make.shared(list=depth165.final.phylip.opti_mcc.list, count=depth165.final.count_table, label=0.03)"
```
The outputs from the clustering were later put into a directory called ```cluster```.

Finally, the OTUs were annotated using Greengenes. SILVA was also considered, but deemed unsatisfactory (see Results). 95% confidence was chosen. The outputs were ```depth165.final.gg.wang.taxonomy``` and ```depth165.final.gg.wang.tax.summary```. The OTUs were then classified and the outputs were ```depth165.final.phylip.opti_mcc.0.03.cons.taxonomy``` and ```depth165.final.phylip.opti_mcc.0.03.cons.tax.summary```, the former of which was used with the .shared file mentioned previously for the Python script provided by Dr. Dill-McFarland to determine taxonomy.
```
classify.seqs(fasta=depth165.final.fasta, count=depth165.final.count_table, template=/home/micb405/data/project_3/databases/gg_13_8_99.fasta, taxonomy=/home/micb405/data/project_3/databases/gg_13_8_99.gg.tax, cutoff=95)
classify.otu(list=depth165.final.phylip.opti_mcc.list, taxonomy=depth165.final.gg.wang.taxonomy, count=depth165.final.count_table, label=0.03, cutoff=95, basis=otu, probs=f)
```
The outputs from annotation and classification, as well as outputs from the python script, were moved into a directory called ```greengenes```. A similar method was done for SILVA for comparison, which was moved into a directory called ```SILVA```.

## Analysis

The python script provided by Dr. Dill-McFarland found [here](https://github.com/EDUCE-UBC/MICB405_project3/blob/master/taxonomy1b.py) was used to determine taxonomy of our samples. This was done using the ```depth165.final.phylip.opti_mcc.0.03.cons.taxonomy``` and ```depth165.final.phylip.opti_mcc.shared```files, and outputted a .txt file that was renamed accordingly. 

Rstudio was also used to graph our results. Rstudio scripts can be found [here](https://github.com/aan1228/Group4_Project3/blob/master/mothur_script.md).

We also changed parameters in the first and second filtering step. These outputs were put into the directory ```2.5_7.5``` and ```25_75```, reflecting their percentile parameter cutoffs (see Results).

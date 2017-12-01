# Assembly and Filter

Set the output directory to ```project3/output```
```
set.dir(output=/home/micb405/Group4/project3/output)
```
Make a file called ```depth165.files```. It will contain all paired FASTA files of all depths, so use a text editor to remove depths that are not 165 metres. Then align the FASTA files and generate a summary of the contigs.
```
make.file(inputdir=/home/micb405/data/project_3, prefix=depth165)
make.contigs(file=depth165.files)
summary.seqs(fasta=depth165.trim.contigs.fasta)
```
Since we expect the 16S V4-V5 region to be 303 bp, we chose cutoffs +/- 10bp from that. We arbitrary? chose 5 and 10 for ambiguous bases and homopolymer counts.
```
screen.seqs(fasta=depth165.trim.contigs.fasta, group=depth165.contigs.groups, maxambig=5, maxhomop=10, minlength=203, maxlength=313)
```

Then we found only unique sequences and determined how many alignments passed quality control.
```
unique.seqs(fasta=depth165.trim.contigs.good.fasta)
count.seqs(name=depth165.trim.contigs.good.names, group=depth165.contigs.good.groups)
summary.seqs(fasta=depth165.trim.contigs.good.unique.fasta, count=depth165.trim.contigs.good.count_table)
```

Alignment was done against the SILVA database of 16S rRNA. ```flip=T``` was chosen to allow mothur to try aligning with the reverse complement. Though this increases the computation required, it would also increase the chances that alignments can be made for more of the contigs.

```
align.seqs(fasta=depth165.trim.contigs.good.unique.fasta, reference=/home/micb405/data/project_3/databases/silva.nr_v128.align, flip=T)
summary.seqs(fasta=depth165.trim.contigs.good.unique.align, count=depth165.trim.contigs.good.count_table)
```

Remove sequences starting after 10370 or ending before 22539. Results were summarized.

```
screen.seqs(fasta=depth165.trim.contigs.good.unique.align, count=depth165.trim.contigs.good.count_table, summary=depth165.trim.contigs.good.unique.summary, start=10370, end=22539)
summary.seqs(fasta=depth165.trim.contigs.good.unique.good.align, count=depth165.trim.contigs.good.good.count_table)

```
Filter to format properly. Remove new duplicates if any arise. PCR amplification introduces around 2-4 errors per 300 bp. diff=4 was chosen because diff=3 and lower caused the chimera.uchime() step to be too long, while diff=5 and greater would result in sequences that were actually different to be grouped together, losing OTUs. Results were summarized.
```
filter.seqs(fasta=depth165.trim.contigs.good.unique.good.align, vertical=T, trump=.)
unique.seqs(fasta=depth165.trim.contigs.good.unique.good.filter.fasta, count=depth165.trim.contigs.good.good.count_table)
pre.cluster(fasta=depth165.trim.contigs.good.unique.good.filter.unique.fasta, count=depth165.trim.contigs.good.unique.good.filter.count_table, diffs=4)
summary.seqs(fasta=depth165.trim.contigs.good.unique.good.filter.unique.precluster.fasta, count=depth165.trim.contigs.good.unique.good.filter.unique.precluster.count_table)
```

chimera.uchime removed chimeric sequences and the results were summarized.
```
chimera.uchime(fasta=depth165.trim.contigs.good.unique.good.filter.unique.precluster.fasta, count=depth165.trim.contigs.good.unique.good.filter.unique.precluster.count_table, dereplicate=t)
remove.seqs(fasta=depth165.trim.contigs.good.unique.good.filter.unique.precluster.fasta, count=depth165.trim.contigs.good.unique.good.filter.unique.precluster.denovo.uchime.pick.count_table, accnos=depth165.trim.contigs.good.unique.good.filter.unique.precluster.denovo.uchime.accnos)
summary.seqs(fasta=depth165.trim.contigs.good.unique.good.filter.unique.precluster.pick.fasta, count=depth165.trim.contigs.good.unique.good.filter.unique.precluster.denovo.uchime.pick.pick.count_table)
```
Singletons were then removed and the results were summarized.

```
split.abund(fasta=depth165.trim.contigs.good.unique.good.filter.unique.precluster.pick.fasta, count=depth165.trim.contigs.good.unique.good.filter.unique.precluster.denovo.uchime.pick.pick.count_table, cutoff=1)
summary.seqs(fasta=depth165.trim.contigs.good.unique.good.filter.unique.precluster.pick.abund.fasta, count=depth165.trim.contigs.good.unique.good.filter.unique.precluster.denovo.uchime.pick.pick.abund.count_table)
```
The resulting file was renamed to avoid confusion, and a distance matrix was constructed with this file. output=lt was chosen to make a phylip formatted lower triangle matrix rather than a column-formatted matrix to save space.
```
system(cp depth165.trim.contigs.good.unique.good.filter.unique.precluster.pick.abund.fasta depth165.final.fasta)
system(cp depth165.trim.contigs.good.unique.good.filter.unique.precluster.denovo.uchime.pick.pick.abund.count_table depth165.final.count_table)
dist.seqs(fasta=depth165.final.fasta, output=lt)
```
Clustering was done using opti. A level of 97% similarity (label=0.03) was chosen to make OTUs at the species level. This parameter was also adjusted.
```
cluster(phylip=depth165.final.phylip.dist, count=depth165.final.count_table, method=opti)"
make.shared(list=depth165.final.phylip.opti_mcc.list, count=depth165.final.count_table, label=0.03)"
```
Finally, the OTUs were annotated using Greengenes. SILVA was also used, but deemed unsatisfactory due to higher number of OTUs un-annotated. 95% confidence was chosen, but this was also changed.
```
classify.seqs(fasta=depth165.final.fasta, count=depth165.final.count_table, template=/home/micb405/data/project_3/databases/gg_13_8_99.fasta, taxonomy=/home/micb405/data/project_3/databases/gg_13_8_99.gg.tax, cutoff=95)
classify.otu(list=depth165.final.phylip.opti_mcc.list, taxonomy=depth165.final.gg.wang.taxonomy, count=depth165.final.count_table, label=0.03, cutoff=95, basis=otu, probs=f)
```

# FOR TESTING DIFFERENT LABELS
cluster(phylip=depth165.final.phylip.dist.test, count=depth165.final.count_table, method=opti, cutoff=0.10)
make.shared(list=depth165.final.phylip.opti_mcc.list.test, count=depth165.final.count_table, label=0.01-0.02-0.03-0.05-0.10)


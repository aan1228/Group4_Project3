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
```

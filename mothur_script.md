# Assembly and Filter

```
set.dir(output=/home/micb405/Group4/project3/output)
make.file(inputdir=/home/micb405/data/project_3, prefix=depth165)
make.contigs(file=depth165.files)
summary.seqs(fasta=depth165.trim.contigs.fasta)
```
|               | Start |  End  |   NBases | Ambigs | Polymer |NumSeqs|
|-|-|-|-|-|-|-|
|Minimum:        1       294     294     0       3       1
2.5%-tile:      1       304     304     0       4       7788
25%-tile:       1       305     305     0       4       77876
Median:         1       305     305     0       4       155752
75%-tile:       1       305     305     0       4       233627
97.5%-tile:     1       306     306     3       6       303715
Maximum:        1       602     602     82      301     311502
Mean:   1       305.028 305.028 0.269321        4.21664
# of Seqs:      311502

minimum=293 maximum 313 (+/- 10)

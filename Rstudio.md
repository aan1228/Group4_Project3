## Creating Figure X

Representative code from the Total Sequences plot is shown.
```R
#Load the data table and ggplot
library(ggplot2)
threshold = read.table('Total.csv', header=TRUE, sep=',')

#Convert data from wide to long
library(tidyr)
new = gather(threshold, Threshold, Count, Low:High)

#Order x-axis
new$Workflow=as.character(new$Workflow)
new$Workflow=factor(new$Workflow, levels=unique(new$Workflow))
new$Threshold=as.character(new$Threshold)
new$Threshold=factor(new$Threshold, levels=unique(new$Threshold))

#Plot paired bar plot
ggplot(new, aes(factor(Workflow), Count, fill=Threshold)) +
  geom_bar(stat='identity', position="dodge") +
  scale_fill_brewer(palette="Set2") +
  ylab("Total Number of Sequences") +
  xlab("Workflow Steps") +
  theme(axis.text.x = element_text(angle=45, vjust=1, hjust=1))
```

## Creating Figure X

```R
#Load the data table and ggplot
library(ggplot2)
phylum = read.table('GG_S_Phylum.csv', header=TRUE, sep=',')

#Convert data from wide to long
library(tidyr)
new = gather(phylum, Database, Count, SILVA:Greengenes)

#Order x-axis
new$Phylum=as.character(new$Phylum)
new$Phylum=factor(new$Phylum, levels=unique(new$Phylum))

#Plot paired bar plot
ggplot(new, aes(factor(Phylum), Count, fill=Database)) +
  geom_bar(stat='identity', position="dodge") +
  scale_fill_brewer(palette="Set2") +
  ylab("log(Number of Sequences)") +
  xlab("Phylum") +
  theme(axis.text.x = element_text(angle=45, vjust=1, hjust=1))
```

## Creating Figure X

Representative code of the Archaea plot is shown below.
```R
#Load the data table and ggplot
library(ggplot2)
archaea = read.table('archaea.tsv', header=TRUE, sep='\t')

#Plot stacked bar plot
ggplot(data = archaea, aes(x = Database, y = Number, fill=Phylum)) + 
  geom_bar(stat="identity") +
  ylab("Number of Sequences")
```

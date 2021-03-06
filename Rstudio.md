## Creating Figure 1: Total and unique sequences after workflow steps

```R
#Load the data table and ggplot
library(ggplot2)
phylum = read.table('workflow.csv', header=TRUE, sep=',')

#Order x-axis
positions <- c("Initial", "Filter 1", "Filter 2", "Pre-clustering", "Chimera Removal", "Singleton Removal")

#Plot paired bar plot
ggplot(phylum, aes(factor(Procedure), Number, fill = Sequence_Type)) +
  geom_bar(stat="identity", position="dodge") +
  xlab("Workflow") +
  ylab("Number of Sequences") +
  scale_fill_brewer(palette="Set2") +
  scale_x_discrete(limits = positions) +
  theme(axis.text.x = element_text(angle=45, vjust=1, hjust=1))
```

## Creating Figure 2: Effect of changing threshold for removing sequences

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

## Creating Figure 3: Comparing annotated and unknown sequences of between Greengenes and SILVA

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
## Creating Figure 4: Comparing annotated phyla using Greengenes or SILVA

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

## Creating Figure 5: Phylum distribution
```R
#Load phylum data
phylum <- 
	read.csv(file.choose())
#Load ggplot
library(ggplot2)

#Reorder phylum groups so they are ordered based on size in the bar
phylum$Phylum <- reorder(phylum$Phylum, phylum$percent)
phylum$Phylum <- factor(phylum$Phylum, levels=rev(levels(phylum$Phylum)))

#Using "wesanderson" colour palettes, expanding for 34 phyla
install.packages("wesanderson")
library(wesanderson)
pal <- wes_palette(34, name = "Darjeeling", type = "continuous")

#Make it into a barplot , using colour palette as created in previous step
pie <- ggplot(data = phylum, aes(x = "", y = percent, fill = Phylum)) + 
	geom_bar(stat="identity", width = 10) + 
	 ylab("Percent") +
	 scale_fill_manual(values = pal)

#Transforming barplot into piechart
pie + coord_polar(theta = "y")
```

## Creating Figure 6: Proteobacteria class distribution

```R
#Inputting proteobacteria class data
class <-  
	read.csv(file.choose())

#Reorder class groups so they are ordered based on size in the bar 
class$Class <- reorder(class$Class, class$percent)
class$Class <- factor(class$Class, levels=rev(levels(class$Class)))

#Creating new palette for the 8 classes of proteobacteria
pal2 <- wes_palette(8, name = "Darjeeling", type = "continuous")

#Create barplot as an object called “pie2”
pie2 <- ggplot(data = class, aes(x = "", y = percent, fill = Class)) + 
	 geom_bar(stat="identity", width = 10) +
	ylab("Percent") +
	scale_fill_manual(values = pal1)

#Creating pie chart from object “pie2”
pie2 + coord_polar(theta = "y")
```
## Creating Figure 7: Genus distribution
```R
#Input data
genus2 <- 
  read.csv(file.choose())
genus2

#Rerordering columns so they are ordered smallest to largest group
genus2$Genus <- reorder(genus2$Genus, genus2$percent)
genus2$Genus <- factor(genus2$Genus, levels=rev(levels(genus2$Genus)))


#Using RColorBrewer to expand colour palettes to accommodate the 45 genus groups
library(RColorBrewer)
colourCount3 = length(unique(genus$Genus))
getPalette3 = colorRampPalette(brewer.pal(9, "Set1"))

#Creating barplot
pie4 <- ggplot(data = genus2, aes(x = "", y = percent, fill = Genus)) + 
  geom_bar(stat="identity") +
  scale_fill_manual(values = getPalette3(colourCount3))

#Creating piechart from barplot
pie4 + coord_polar(theta = "y")
```

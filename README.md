Last Updated: 02/14/2016

# IsoView
Visualization and clustering of DNA transcript data

## Project Background

IsoView is a program that [Jack Fu](http://jfubiostats.com/) developed while he was a contractor at PacBio in summer 2015. Specifically, I asked Jack to help me automatically cluster isoforms into groups in a single gene. 

With Iso-Seq, particularly in targeted sequencing, we often see a lot of isoforms, sometimes up to the hundreds, and it was becoming hard to see if particular exon skipping patterns were present. Furthermore, I wanted to remove all the long introns that were always spliced out in the visualization. So I asked Jack to create IsoView so help with that. The clusters are created by doing a Kmeans clustering where the distances are the similarities in shared exons between the isoforms. 

The tool is pretty simple but has helped in our targeted Iso-Seq projects. If you have any ideas for improving it, please let us know!


## Getting it to run on R

You will need the [bioconductor package](https://www.bioconductor.org/install/) in addition to pulling this from GitHub. This can all be done within R. I confess I'm not a R expert (I just use ggplot a lot...) so if there are better ways to install let me know.

```
## install bioconductor 
## you also may have to upgrade it
## try http:// if https:// URLs are not supported
source("https://bioconductor.org/biocLite.R")
biocLite()
biocLite("BiocUpgrade")

## install IsoView, set force=TRUE if you need to update
library("devtools")
install_github("Magdoll/IsoView", force=TRUE)  
```

To use it:

```
library(IsoView)
visualize('test.gff', clust_count=5, out_png='test.png', out_report='test.report.txt')
```

You will need to provide the number of clusters `clust_count`. The output figure will be drawn and the transcript cluster membership will be written to the report file.


### Input GFF requirement

The input GFF should be in the following format. Most importantly, there must be a unique **transcript_id** for each transcript. Otherwise the code will break. 

```
chrX	PacBio	transcript	147014204	147030361	.	+	.	gene_id "Iso1"; transcript_id "Iso12";
chrX	PacBio	exon	147014204	147014282	.	+	.	gene_id "Iso1"; transcript_id "Iso12";
chrX	PacBio	exon	147018023	147018132	.	+	.	gene_id "Iso1"; transcript_id "Iso12";
chrX	PacBio	exon	147018985	147019119	.	+	.	gene_id "Iso1"; transcript_id "Iso12";
chrX	PacBio	exon	147022095	147022181	.	+	.	gene_id "Iso1"; transcript_id "Iso12";
chrX	PacBio	exon	147026463	147026571	.	+	.	gene_id "Iso1"; transcript_id "Iso12";
chrX	PacBio	exon	147027054	147027136	.	+	.	gene_id "Iso1"; transcript_id "Iso12";
chrX	PacBio	exon	147030203	147030361	.	+	.	gene_id "Iso1"; transcript_id "Iso12";
```


### Running through an example

We will use the example [GFF file](https://github.com/Magdoll/IsoView/blob/master/example/suppEid_FMR1.collapsed.gff):

```
library(IsoView)
visualize('suppEid_FMR1.collapsed.gff', clust_count=4, out_png='test.png', out_report='test_report.txt')
```

The output figure should look like this:

![](https://dl.dropboxusercontent.com/u/47842021/wiki_transcriptome/IsoView_example/isoview_out.png)

And the report file shows the clustering result:

```
transcript_id	cluster
Iso1	4
Iso10	3
Iso10b	3
Iso11b	3
Iso12	3
Iso13	4
Iso14	4
Iso15	4
Iso17	2
Iso18	2
Iso19	1
Iso20	3
Iso3	4
Iso7	2
Iso8	2
Iso9	1
```

Cluster #1 is black, #2 is red, then green and blue (so it starts from the bottom counting up).


### Extracting the groups into separate GFFs

Again, I'm a R rookie, but here's something I would do after I get the clusters.

```
library(rtracklayer)

# I had to add the group attribute to the GFF object for the write out to display correctly on IGV
gff <- import.gff('suppEid_FMR1.collapsed.gff')
gff$group <- paste("transcript_id ", '"', gff$transcript_id, '";', sep='')


x <- read.table('isoview_out.txt',sep='\t', header=T)
gff1 <- subset(gff, transcript_id %in% x[x$cluster==1,"transcript_id"])
export.gff(gff1, "cluster1.gff")
gff2 <- subset(gff, transcript_id %in% x[x$cluster==2,"transcript_id"])
export.gff(gff2, "cluster2.gff")
gff3 <- subset(gff, transcript_id %in% x[x$cluster==3,"transcript_id"])
export.gff(gff3, "cluster3.gff")
gff4 <- subset(gff, transcript_id %in% x[x$cluster==4,"transcript_id"])
export.gff(gff4, "cluster4.gff")
```

Now I can import this into IGV and visualize it with the common introns displayed:

![](https://dl.dropboxusercontent.com/u/47842021/wiki_transcriptome/IsoView_example/Screenshot%202016-02-13%2017.55.31.png)


This sample data is a subset of isoforms taken from this paper: [Tassone et al., "Differential increases of specific FMR1 mRNA isoforms in premutation carriers", *J Med Genet (2015)*](http://www.ncbi.nlm.nih.gov/pubmed/25358671). Figure 5 shows a manual grouping of the isoforms. You can see that IsoView mostly replicated the human groupings --- it split group C (Iso9, 19, 7, 8, 17, 18) into cluster #1 and #2, but put all of group D into cluster #3 and clusterA into cluster #4. I don't think IsoView is intended to completely automate the grouping process, rather, it helps researchers inspect the difference in each groups, which they can then further refine those definitions.

I have used IsoView in my own projects where I then followed up with manual definitions of groupings. Right now those scripts are in Python but I will see if I can put them up as R scripts later. 

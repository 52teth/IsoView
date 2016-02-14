# IsoView
Visualization and clustering of DNA transcript data

## Project Background

IsoView is a program that [Jack Fu](http://jfubiostats.com/) developed while he was a contractor at PacBio in summer 2015. Specifically, I asked Jack to help me automatically cluster isoforms into groups in a single gene. 

With Iso-Seq, particularly in targeted sequencing, we often see a lot of isoforms, sometimes up to the hundreds, and it was becoming hard to see if particular exon skipping patterns were present. Furthermore, I wanted to remove all the long introns that were always spliced out in the visualization. So I asked Jack to create IsoView so help with that.


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

'''
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
'''

Cluster #1 is black, #2 is red, then green and blue (so it starts from the bottom counting up).


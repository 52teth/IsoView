# IsoView
Visualization and clustering of DNA transcript data

## Project Background

IsoView is a program that [Jack Fu](http://jfubiostats.com/) developed while he was a contractor at PacBio in summer 2015. Specifically, I asked Jack to help me automatically cluster isoforms into groups in a single gene. 

With Iso-Seq, particularly in targeted sequencing, we often see a lot of isoforms, sometimes up to the hundreds, and it was becoming hard to see if particular exon skipping patterns were present. 


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
visualize('test.gff', 5, out_png='test.png', out_report='test.report.txt')
```


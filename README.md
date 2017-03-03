# Convert accession numbers to taxonomy

[![Build Status](https://travis-ci.org/sherrillmix/taxonomizr.svg?branch=master)](https://travis-ci.org/sherrillmix/taxonomizr)
[![codecov.io](https://codecov.io/github/sherrillmix/dnaplotr/taxonomizr.svg?branch=master)](https://codecov.io/github/sherrillmix/taxonomizr?branch=master)

## Introduction

`taxonomizr` provides a some simple functions to parse NCBI taxonomy and accession dumps and use them to assign taxonomy to accession numbers.

## Installation
To install directly from github, use the [<code>devtools</code>](https://github.com/hadley/devtools) library and run:

```r
devtools::install_github("sherrillmix/taxonomizr")
```

## Examples

### Preparation

To use the library, include it in R:

```r
library(taxonomizr)
```

Then download the necessary names and nodes files from NCBI:

```r
getNamesAndNodes()
```

```
## [1] "./names.dmp" "./nodes.dmp"
```

And download accession to taxa id conversion files from NCBI (this is a _big_ download):

```r
#this is a big download
getAccession2taxid()
```

```
## [1] "./nucl_gb.accession2taxid.gz"  "./nucl_est.accession2taxid.gz"
## [3] "./nucl_gss.accession2taxid.gz" "./nucl_wgs.accession2taxid.gz"
```

If you would also like to identify protein accession numbers also download the prot file from NCBI (again this is a _big_ download):

```r
#this is a big download
getAccession2taxid(types='prot')
```

```
## [1] "./prot.accession2taxid.gz"
```



Then process the downloaded accession files into a more easily accessed form (this could take a while):


```r
read.accession2taxid(list.files('.','accession2taxid.gz$'),'accessionTaxa.sql')
```

```
## Reading nucl_est.accession2taxid.gz.
```

```
## Reading nucl_gb.accession2taxid.gz.
```

```
## Reading nucl_gss.accession2taxid.gz.
```

```
## Reading nucl_wgs.accession2taxid.gz.
```

```
## Reading in values. This may take a while.
```

```
## Adding index. This may also take a while.
```

```
## [1] TRUE
```


Now everything should be ready for processing. This should all be cached locally and so is not required every time. It is not necessary to manually check for the presence of these files since the functions automatically check to see if their output is present and if so skip downloading/processing. Delete the local files if you would like to redownload or reprocess them.

### Finding taxonomy for NCBI accession numbers

First, load the nodes and names files into memory:


```r
taxaNodes<-read.nodes('nodes.dmp')
taxaNames<-read.names('names.dmp')
```


Then we are ready to convert NCBI accession numbers to taxonomic IDs. For example, to find the taxonomic IDs associated with NCBI accession numbers "LN847353.1" and "AL079352.3":

```r
taxaId<-accessionToTaxa(c("LN847353.1","AL079352.3"),"accessionTaxa.sql")
print(taxaId)
```

```
## [1] 1313 9606
```

And to get the taxonomy for those IDs:


```r
getTaxonomy(taxaId,taxaNodes,taxaNames)
```

```
##      superkingdom phylum       class      order            
## 1313 "Bacteria"   "Firmicutes" "Bacilli"  "Lactobacillales"
## 9606 "Eukaryota"  "Chordata"   "Mammalia" "Primates"       
##      family             genus           species                   
## 1313 "Streptococcaceae" "Streptococcus" "Streptococcus pneumoniae"
## 9606 "Hominidae"        "Homo"          "Homo sapiens"
```

### Finding taxonomy for taxonomic names

If you'd like to find IDs for taxonomic names then you can do something like:

```r
taxaId<-getId(c('Homo sapiens','Bos taurus','Homo'),taxaNames)
print(taxaId)
```

``` 
## [1] "9606" "9913" "9605"
```

And again to get the taxonomy for those IDs use `getTaxonomy`:


```r
getTaxonomy(taxaId,taxaNodes,taxaNames)
```

```
##      superkingdom phylum     class      order      family      genus 
## 9606 "Eukaryota"  "Chordata" "Mammalia" "Primates" "Hominidae" "Homo"
## 9913 "Eukaryota"  "Chordata" "Mammalia" NA         "Bovidae"   "Bos" 
## 9605 "Eukaryota"  "Chordata" "Mammalia" "Primates" "Hominidae" "Homo"
##      species       
## 9606 "Homo sapiens"
## 9913 "Bos taurus"  
## 9605 NA
```

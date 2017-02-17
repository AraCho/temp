# GoodMethod
short description of GoodMethod

## Table of contents
- [Calling presence/absence of CNVs](#Calling_CNV)
- [Phylogenetic tree contruction](#Constructing_Tree)
- [Co-expression network generation](#CX_Net)
- [Correlation of RNA-seq and exome-Seq](#Corr)
- [False discovery rate estimation: 10x Cross validation](#10x)
- [False discovery rate estimation: Empirical testing](#Empirical)


## <a id="Calling_CNV"></a> Calling presence/absence of CNVs
### Requirement
  * Linux/Unix environment (Python/Perl)
  * beanplot (R package)
  * samtools
  * bedtools  

### Config file
Adjusting __GoodMethod.cfg__ can change the belows.
  * Path to python/samtools/bedtools/Rscript
  * The threshold of mapping-qualities/read-count for alignment files
  * FDR for CNV calling

### Running

  ```
  cd ./GoodMethod/
  bash run_GoodMethod.sh [directory for tumor] [directory for normal] [.bed file for CNV segments] [base name]
  ```
  * __[directory for tumor]__: path to directory which aligned bam files of __tumor__ single cells are in.
    
  * __[directory for normal]__: path to directory which aligned bam files of __normal__ control single cells are in.  
   
  * __[.bed file for CNV segments]__: tab-delimited bed file of CNV segments from exom sequencing.
  
      ```
      [chromosome]	[start]	[end]	[chromosome:start:end:CNV]
      ```
      
      __Note: the 4th column of the file must have the exact same format with that of below.
        __ (__Amp__:amplification, __Del__:deletion)
    * example
  ```
  7   19533   157408385   7:19533:157408385:Amp
  9   19116859    32405639    9:19116859:32405639:Del
  ```
  * __[base name]__ : base name for output directory
  

### Output
The directory __output_[base name]__ would be generated and all the output files would be located in this directory.
  1. __incidenceMatrix.csv__: the matrix of the presence/absence of the CNVs in individual single cells
  2. __pdf__: 
    1. Read-count distribution in CNV segments. (violin plot)
    2. Hierarchical clustering of the tumor single cells by the presence/absence of the CNVs.

![violin](images/GoodMethod_violin.jpg?raw=true "violin" )
![dendrogram](images/GoodMethod_dendrogram.jpg?raw=true "dendrogram" )


## <a id="Constructing_Tree"></a> Phylogenetic tree contruction
Phylogenetic trees could be generated by the presence/absence profiles of CNV. This requires additional R package (Rphylip) for execution.

### Requirement
  * Rphylip (R package) with Phylip 

### Config file

Adjusting __Tree.cfg__ can change the belows.
  * Path to Rscript
  * Path to Rphylip
  
### Running
  * Before running, the path to phylip need to be added in __Tree.cfg__ file.
```
bash run_Tree.sh [CNV presence/absence matrix][number of genotypes] [base name for output file]
```
  * __[CNV presence/absence matrix]__: .incidenceMatrix.csv files. Note that exclusion of non-malignent single cells from the matrix is recommended. 
  * __[number of genotypes]__: the number of genotypes which are used for constructing trees by the aid of hierarchical clustering from the previous output files.
  * __[base name]__ : base name for output directory

### Output
__[base name]_cluster.pdf__ (phylogenetic trees) would be generated in __output_[base name]__ directory. 
![tree](images/Tree_tree.jpg?raw=true "tree" )

## <a id="CX_Net"></a> Generating co-expression network

### Requirement
### Config file
### Running

  ```
  ```
### Output

## <a id="Corr"></a> Correlation of RNA-seq and exome-Seq

### Requirement
### Config file
### Running

  ```
  ```
### Output

## <a id="10x"></a> False discovery rate estimation: 10x Cross validation

### Requirement
### Config file
### Running

  ```
  ```
### Output

## <a id="Empirical"></a> False discovery rate estimation: Empirical testing

### Requirement
### Config file
### Running

  ```
  ```
### Output


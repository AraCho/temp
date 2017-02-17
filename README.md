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
Co-expression network could be constructed by the correlations of expressions in single cells.
  

### Requirement
  * scde, PCIT, boot, parallel, raster, flashClust (R package)
  
### Config file
Adjusting __CorrelationNetwork.cfg__ can change the belows.
  * Path to Rscript
  * ncore: Number of cores for computation  (default: 12)
  * cor_threshold: Starting threshold for neighbour correlationof gene of interest (is decreased until at least x neighbours are found)(default: 0.9)
  * min_neighbours: How many direct neighbours of gene of interest should be searched for (default: 20)
  * minRawReads: How many raw reads should at least map to a gene to consider the gene (default: 100)
  * percentCellsExpressing: Percentage (0.15 =15%) of cells expressing a gene to consider the gene (default: 0.15)
  * minGenesExpr: How many genes should be expressed at least in a cell to consider the cell (default: 800)
  * depth: How deep should the gene analysis search. (2=only direct neighbor genes would be considered) (default: 2)


### Running

  ```
  bash run_CorrelationNetwork.sh [input matrix] [centered gene] [base name]
  ```
  
  * __[input matrix]__: tab-delimited file of the number of mapped reads to each gene in single cells. 
  * __[centered gene]__: a target gene of which neighbor genes are analyzed.  
  * __[base name]__ : base name for output directory

### Output
__[base name]_cluster.pdf__ (phylogenetic trees) would be generated in __output_[base name]__ directory. 
![CXnet](images/.jpg?raw=true "CXnet" )



## <a id="Corr"></a> Correlation of scRNA-seq and exome-Seq
The correlation between Exome-seq and single-cell RNA-seq could be assessed. 
### Requirement
  * zoo (R package)
  
### Config file
Adjusting __CompareExomeSeq_vs_ScRNAseq.cfg__ can change the belows.
  * Path to Rscript
  * window size
  
### Running

  ```
  bash run_compareExomeSeq_vs_ScRNAseq.sh [matrix for read counts] [base name for output file]
  ```
  * __[matrix for read counts]__: tab-delimited file of the number of mapped reads to each gene in Exome-seq and single-cell RNA-seq
  
      ```
      [gene] [chromosome] [start] [#read in Exome-seq(normal)] [#read in Exome-seq(tumor)] [#read in scRNA-seq(normal)] [#read in scRNA-seq(tumor)]
      ```
    * example
  ```
  
DDX11L1   1   11874   538   199   5   0
WASH7P    1   14362   4263   6541   223   45
  ```
  * __[base name]__ : base name for output directory



### Output
__Compare_[window_size].pdf__ (Box plot) would be generated in __output_[base name]__ directory. 
![compare](images/Compare_200.jpg?raw=true "compare" )


## <a id="10x"></a> False discovery rate estimation: 10x Cross validation
The FDR could be estimated by 10x cross validation

### Requirement
  * beanplot (R package)
  * samtools
  * bedtools  


### Config file
Adjusting __10X_cross_validation.cfg__ can change the belows.
  * Path to python/samtools/bedtools/Rscript
  * The threshold of mapping-qualities/read-count for alignment files
  * FDR for CNV calling

### Running

  ```
  bash run_10X_cross_validation.sh [directory for test] [.bed files for CNV segments] [base name]
  ```
  * __[directory for test]__: path to directory which aligned bam files of __testing__ for FDR estimation
   
  * __[.bed file for CNV segments], [base name]__ : same as above.

### Output
Box plot of FDRs would be generated (__[base name]_boxplot.pdf__) in  __output_[base name]__ directories.
![10X](images/10X_boxplot.jpg?raw=true "10Xval_Test" )

## <a id="Empirical"></a> False discovery rate estimation: Empirical testing
The FDR could be estimated by empirical testing

### Requirement
  * beanplot (R package)
  * samtools
  * bedtools  
  
### Config file
Adjusting __Empirical_validation.cfg__ can change the belows.
  * Path to python/samtools/bedtools/Rscript
  * The threshold of mapping-qualities/read-count for alignment files
  * FDR for CNV calling

### Running

  ```
  bash run_empirical_validation.sh [directory for train] [directory for test] [.bed files for CNV segments] [base name]
  ```

  * __[directory for train]__: path to directory which aligned bam files for __training__ for FDR estimation
    
  * __[directory for test]__: path to directory which aligned bam files of __testing__ for FDR estimation
   
  * __[.bed file for CNV segments], [base name]__ : same as above.

### Output
Box plot of FDRs would be generated (__[base name]_boxplot.pdf__) in  __output_[base name]__ directories.
![empirical](images/Empirical_boxplot.jpg?raw=true "empirical_Test" )

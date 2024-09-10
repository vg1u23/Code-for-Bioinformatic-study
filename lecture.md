PICRUST2 PIPELINE

1. Access the server
2. reactivate the Qiime environment

reminder: you can look up everything within the conda environment with
```
conda info --envs
```

```
conda activate qiime2-amplicon-2024.2
```

3. Data pre-processing
   the table gets filtered to reduce the runtime and background noise

   ```
   qiime feature-table filter-features \
   --i-table direction/filename.qza \
   --p-min-frequency 50 \
   --p-min-samples 2 \
   --o-filtered-table final_table_filtered.qza

description:
--p-min-frequency command filters out features that have fewer than 50 counts across all samples. Features that occurs less than 50 times will be retained.
--p-min-sample: filters out features that appear in fewer than 2 samples


 4. FILTER: Now we’ll filter the sequences to only include those that are in our new filtered table (final_table_filtered.qza):

```
qiime feature-table filter-seqs \
  --i-data PRJEB40733_10s-rep-seqs.qza \
  --i-table final_table_filtered.qza \
  --o-filtered-data  representative_sequences_final_filtered.qza
  ```


5. Exporting: Then we can export these files - first the feature table, which we’ll export and then convert from .biom format to .txt format:

```
qiime tools export \
  --input-path final_table_filtered.qza \
  --output-path exports_filtered
  
biom convert \
  -i exports_filtered/feature-table.biom \
  -o exports_filtered/feature-table.txt \
  --to-tsv
```

Then we can export the sequences file:

```
qiime tools export \
  --input-path representative_sequences_final_filtered.qza \
  --output-path exports_filtered
```

This will be exported as a .fasta file, which you can look at in the exports_filtered folder, if you like.
Finally, we’ll export the taxa classifications that we have, we’ll slightly modify the resulting file, and then deactivate the conda environment:

```
qiime tools export \
>   --input-path PRJEB40733_gg2.taxonomy.qza \
>   --output-path taxa
sed -i -e '1 s/Feature/#Feature/' -e '1 s/Taxon/taxonomy/' taxa/taxonomy.tsv
```

6. Deactivate conda environment
```
conda deactivate
```

7. Activate Picrust2
   
```
conda activate picrust2
```

8. Run the whole Picrust2 pipeline
```
   picrust2_pipeline.py \
  -s exports_filtered/dna-sequences.fasta \
  -i exports_filtered/feature-table.biom \
  -o picrust2_out_pipeline_filtered \
  -p 4 \
  -t sepp
```









USE R FOR DOWNSTREAM ANALYSIS

9. Start R with R Studio
    
11. Set working directory

First check in which working directory you are in
```
getwd()
```

Set the working directory to the folder containing all of the information
For example: setwd(C:/Users/vg1u23/OneDrive - University of Southampton/Desktop/Picrust_20_08_2024)

```
setwd()
```

12. Install all the packages

First, ensure BiocManager is installed, if BiocManager is already installed, the code silently does nothing and moves on to the next lines of the script
```
if (!requireNamespace("BiocManager", quietly = TRUE)) {
  install.packages("BiocManager")
}
```
To install the latest development version of ggpiscrust2 from GitHub               
Install the devtools package if not already installed  
```
install.packages("devtools")
```              

Install ggpicrust2 from GitHub  

```
devtools::install_github("cafferychen777/ggpicrust2")
```

Load BiocManager and use a for loop to install all the packages
```
if (!requireNamespace("BiocManager", quietly = TRUE))
  install.packages("BiocManager")

pkgs <- c("phyloseq", "ALDEx2", "SummarizedExperiment", "Biobase", "devtools", 
          "ComplexHeatmap", "BiocGenerics", "BiocManager", "metagenomeSeq", 
          "Maaslin2", "edgeR", "lefser", "limma", "KEGGREST", "DESeq2")

for (pkg in pkgs) {
  if (!requireNamespace(pkg, quietly = TRUE))
    BiocManager::install(pkg)
}
```






13. Load packages
```
  library(phyloseq)
  library(ggplot2)
  library(phyloseq)
  library(ALDEx2)
  library(SummarizedExperiment)
  library(Biobase)
  library(devtools)
  library(ComplexHeatmap)
  library(BiocGenerics)
  library(BiocManager)
  library(metagenomeSeq)
  library(Maaslin2)
  library(edgeR)
  library(lefser)
  library(limma)
  library(KEGGREST)
  library(DESeq2)
```


14. Read in the pathway table to R
    ```
    pwy_table <- read.csv(paste("picrust2_out_pipeline/pathways_out/path_abun_unstrat.tsv/path_abun_unstrat.tsv", sep=""), sep='\t', header=T, check.names=FALSE)
    ```
  Note: check.names=FALSE, prevents R from automatically modifying column and row names

15. Have a look at the table by calling it
```
pwy_table
``
Regarding the pathway table: you see a table with the annotated pathway or pathway ID, samplename as well as the pathway abundance in each sample

To investigate the table:
```
dim(pwy_table) #columns and rows
str(pwy_table) #structure of the table
head(pwy_table) #shows the first 6 rows of the table
tail(pwy_table) #shows the last 6 rows of the table
ncol(pwy_table) #columns
nrow(pwy_table) #rows
```

16. Manipulation of the table
pwy_table_num = data.matrix(pwy_table[,2:25]) #convert the ASV table to a numeric matrix
rownames(pwy_table_num) = pwy_table[,1] #give the matrix row names

17. Read in the metadata into R, sep='\t' specifies that it is tab separated
```
metadata <- read.csv(paste("metadata.tsv", sep=""), sep='\t', check.names=FALSE)
```

Note: checknames FALSE is quite important if your sampleIDs start with numbers
In general R would modify the columns by putting an X infront of the ID, if this happens the phyloseq object wont work



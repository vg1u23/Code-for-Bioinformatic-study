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

``




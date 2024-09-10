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
   --o-filtered-table final_table_filtered.qza ```

description:
--p-min-frequency command filters out features that have fewer than 50 counts across all samples. Features that occurs less than 50 times will be retained.
--p-min-sample: filters out features that appear in fewer than 2 samples


FILTER: Now we’ll filter the sequences to only include those that are in our new filtered table (final_table_filtered.qza):

```
qiime feature-table filter-seqs \
  --i-data PRJEB40733_10s-rep-seqs.qza \
  --i-table final_table_filtered.qza \
  --o-filtered-data  representative_sequences_final_filtered.qza
  ```


Then we can export these files - first the feature table, which we’ll export and then convert from .biom format to .txt format:

```
qiime tools export \
  --input-path final_table_filtered.qza \
  --output-path exports_filtered
  
biom convert \
  -i exports_filtered/feature-table.biom \
  -o exports_filtered/feature-table.txt \
  --to-tsv
```





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
  
  ```

description:
--p-min-frequency command filters out features that have fewer than 50 counts across all samples. Features that occurs less than 50 times will be retained.
--p-min-sample: filters out features that appear in fewer than 2 samples



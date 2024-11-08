use fastq-dl to download
read in with manifest file
use demux to visualize
cutadapt to get rid of primers
demux to visualize again



qiime vsearch merge-pairs   --i-demultiplexed-seqs Mitter_woprimer.qza   --o-merged-sequences Mitter_B_2017_16S-trimmed-joined.qza --verbose

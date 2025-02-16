# Code-for-Bioinformatic-study
Bioinformatic meta-analysis focused on the seed microbiome

Date 22.04.2024

#Useful MobaxTerm/bash commands
#list all modules
module availe 
#always work in the scratch section
cd /scratch/
cd username

#You can also run conda info --envs, and that will show the paths to all your environments.
conda run --info
ls

#to look quickly at some files
cat PRJAXXXX>=.cv

#Displaying current working directory
pwd

copy data from the online mobax iridis environment into another environment (ssd, onedrive etc), be careful abou the direction of the slashes /,\
scp /scratch/vg1u23/PRJNA916854.csv /c/Users/vg1u23/OneDrive\ -\ University\ of\ Southampton/Desktop/PhD_Gschiel/Project/Project1_Bioinformatics/studies_metadata/

#How to load qiime2
conda activate qiime2-amplicon-2024.2

#Create a folder
mkdir "foldername"

#download metadata, via edirect and esearch function
#download the programme via wget function 
wget -q https://ftp.ncbi.nlm.nih.gov/entrez/entrezdirect/install-edirect.sh -O -
conda info --envs
rsh -c "$(wget -q https://ftp.ncbi.nlm.nih.gov/entrez/entrezdirect/install-edirect.sh -O -)"
export PATH=/home/vg1u23/edirect:\${PATH}}/edrect:${PATH}
conda create -n edirect
ls
conda activate edirect
conda install bioconda::entrez-direct
esearch -db sra -query PRJNA916854 | efetch -format runinfo > PRJA916854.csv

#move a document into a folder
mv PRJA916854.csv /scratch/vg1u23/bioinfo_analysis

#remove a doc
rm "docname"

#remove a folder with something inside:
rm -r "foldername"


#Strategy: Use the edirect plugin for retrieving metadata, download the csv files (go into your scratch folder and use the download function) and store them also in OneDrive, get the SRP Number from the metadata files (study number) and retrieve the sequence data with pysradb. Store everything together in a folder. Check if the download worked and the file is not empty.

#end qiime2 etc.
conda deactivate

#check if the slurm file sbatch is running
squeue -u "username"
#R=running, PD=pending
#check the environment for plugins
conda info --env



#download metadata and raw sequences
#donwload in scratch, save raw data on ssd
fastq-dl -a PRJNA438294 --outdir PRJNA438294 --cpus 5 

#create a manifest file
#use qiime2 import function

qiime tools import --type 'SampleData[PairedEndSequencesWithQuality]' 
--input-path import_manifest_cluster 
--input-format PairedEndFastqManifestPhred33 
--output-path /home/eo2r24/QIIME2_TomaSeed_Olimi/TomatoSeed1000_demux.qza

#single reads
qiime tools import \
--type 'SampleData[SequencesWithQuality]' \
--input-path plate_1_manifest_file.tsv \
--output-path single-end-demux.qza \
--input-format SingleEndFastqManifestPhred33V2


Naive Bayes Classifier: 
A pre-trained classifier was downloaded from the website: https://github.com/qiime2/resources/blob/main/index.md with the download link: https://data.qiime2.org/classifiers/sklearn-1.4.2/silva/silva-138-99-nb-classifier.qza. 
Full 16S backbone will be used in order to analyse datasets based on different primers/16S regions.
Details: 
:header: **Silva 138 99% OTUs full-length sequences**
**Download**: [Silva 138 99% OTUs full-length sequences](https://data.qiime2.org/classifiers/sklearn-1.4.2/silva/silva-138-99-nb-classifier.qza)\
**UUID**: 70b4b5f4-8fce-40bd-b508-afacbc12a5ed\
**SHA256**: c08a1aa4d56b449b511f7215543a43249ae9c54b57491428a7e5548a62613616\
**Sklearn Version**: 1.4.2\
**Date Trained**: 2024-05-30\
**Notes**: [Silva species taxonomy may be unreliable](#Silva)\
**Citations**: @Robeson2020-ax, @Bokulich2018-yb, [Silva](#Silva-Citations)

Check: 
Qiime version should be compatible with the classifier!
Integrity of the file: After downloading the classifier the sha256 sum of the classifier using the command shasum -a 256 <path-to-classifier>  should be checked. The output should match the SHA256 value associated with the classifier on the website. IF it does
not match do not use the classifier and re-download it from the website. 
Note: Shasum is not a built in command in powershell. Use the command:  "Get-FileHash -Algorithm SHA256 "C:\Users\vg1u23\Downloads\silva-138-99-nb-classifier.qza"" instead.
output
Algorithm       Hash                                                                   Path
---------       ----                                                                   ----
SHA256          C08A1AA4D56B449B511F7215543A43249AE9C54B57491428A7E5548A62613616       C:\Users\vg1u23\Downloads\sil...
They DO match!!!!

Consideration:  Check some small dataset (of each 16S region v1-v3, v3-v4, v4 and v5-v7) with both the 16S full backbone pre-trained classifier and a trained one on the specific region. 
Compare the results. Is there any change? 


#strategy change from paired only to single end (both paired and single end studies are included. However, only forward reads are processed.)
removed all the reverse reads with the code: 
in case of studies with joined reads, forward and reverse reads the command was used additionally to the rm *_2.fastq.gz command to remove the joined ones:
find . -maxdepth 1 -type f -name "*.fastq.gz" ! -name "*_1.fastq.gz" -delete
ls
print function was used to check first: 
find . -maxdepth 1 -type f -name "*.fastq.gz" ! -name "*_1.fastq.gz" -print
ls

it lists the files which are deleted

In case of some paired end studies the sequence uploaded to SRA is interleaved (joined already)
Seqtk needs to be used to separate the reads then (PRJNA358488): 

for file in *.fastq.gz; do
    base=$(basename "$file" .fastq.gz)
    seqtk seq -l0 -1 "$file" > "${base}_1.fastq.gz"  # Forward reads
    seqtk seq -l0 -2 "$file" > "${base}_2.fastq.gz"  # Reverse reads
done

check the files again: 
head -n 10 SRR5527439_1.fastq.gz
head -n 10 SRR5527439_2.fastq.gz

the forward reads should begin with 1/1 and have read 3/1 and 5/1 reverse reads should begin with 2/1 
For example:
@SRR5527439.1 1/1
ACGTAGCGGACTACACGGGTATCTAATCCCATTCGCTCCCCTAGCTTTCGTCTCTTAGTGTCAGTGTCGGCCCAGCAGAGTGCTTTCGCCGTTGGTGTTCTTTCCGATCTCTACGCATTTCACCGCTCCACCGGAAATTCCCTCTGCCCCTACCGTACTCAAGCTTGGTAGTTTCCACCGCCTGTCCAGGGTTAAGCCCTGGGATTTGACGGCGGACTTAAAAAGCCACCTACAGACGCTTTACGCCCAATCATTCCGGATAACGCTTGCATCCTCTGTATTACCGCGGCGGCTGGCACGCTACGT
+
CCCCCFCCCCCCGGGGGGGGGGHHHHHHHHHHHHHGGHGGGHGHHHHHHGHGHHH##HH##H#H###H##GGGGHHHH####HHH#HGG#GG#GGEGH#HHH#HGGE#GHHH#H#GGGG#HHHH#GGGG#HHG#GGGH#HHH#HHGGHHGH#GHGG#GHHGFGHHHHGHH#HHHHHHHHGGGGGHGHHHGHGHHHGGGHHHGHHHGGGGGGFEFGHHGHHHGHHHGHHHHGHGGGFGFFHEGGGHHHHHHFHGFEFFHHHFGEGHHHHHHGHGGHHHHFHFEBGGGGGGGGGGGBBBBBBFBABA?
@SRR5527439.3 3/1
ACGTAGCGTGCCAGCCGCGGTAATACAGAGGATGCAAGCGTTATCCGGCATGATTGGGCGTAAAGCGTCTGTAGGTGGCTTTTTAAGTCCGCCGTCAAATCCCAGGGCTTAACCCTGGACAGGCGGTGGAAACTACCAAGCTTGAGTACGGTAGGGGCAGAGGGAATTTCCGGTGGAGCGGTGAAATGCGTAGAGATCGGAAAGAACACCAACGGCGAAAGCACTCTGCTGGGCCGACACTGACACTAAGAGACGAAAGCTAGGGGAGCGAATGGGATTAGAAACCCCGGTAGTCCGCTACGT
+
>AA1>?ADAAAAFBFGG?AEE0FHGHBGGEFHGHFBFGGGE?/CG1E//AEF#HH#ECFF#E?/21#EEEGF@FHGFFHHFGH#99G>GEEGG/EF#AF#FGHFHF?AHHHFHGCCGEFGCEACCG>9;0;99<<CG0HHC#<CG:=GCCCEEFC<:C#9EHHD#<<90#?HHFCCCCCHF#1<?9/CCFB#B#CCC#HH#FF2CEF<#;#><#AEG?/>>>##0F#GA?GGG#EBHDG#2FD@D#1#G##GGE1BGGB0CHGGE/CGHHGG1HHGF22EGAAAEE00GGEDA1DAA1A>A>1
@SRR5527439.5 5/1
ACGTAGCGGACTACAAGGGTATCTAATCCCATTCGCTCCCCTAGCTTTCGTCTCTCAGTGTCAGTGTCGGCCCAGCAGAGTGCTTTCGCCGTTGGTGTTCTTTCCGATCTCTACGCATTTCACCGCTCCACCGGAAATTCCCTCTGCCCCTACCGTACTCAAGCTTGGTAGTTTCCACCGCCTGTCCAGGATTGAGCCCTGGGATTTGACGGCGGACTTAAAAAGCCACCTACAGACGCTTTACGCCCAATCATTCCGGATAACGCTTGCATCCTCTGTATTACCGCGGCGGCTGGCACGCTACGT
(seqtk-env) [vg1u23@login6002 PRJNA358488]$ head -n 10 SRR5527439_2.fastq.gz
@SRR5527439.2 2/1
ACGTAGCGTGCCAGCCGCCGCGGTAATACAGAGGATGCAAGCGTTATCCGGAATGATTGGGCGTAAAGCGTCTGTAGGTGGCTTTTTAAGTCCGCCGTCAAATCCCAGGGCTCAACCCTGGACAGGCGGTGGAAACTACCAAGCTGGAGTACGGTAGGGGCAGAGGGAATTTCCGGTGGAGCGGTGAAATGCGTAGAGATCGGAAAGAACACCAACGGCGAAAGCACTCTGCTGGGCCGACACTGACACTGAGAGACGAAAGCTAGGGGAGCGAATGGGATTAGAAACCCGAGTAGTCCTAGTCTG
+
ABBBBCADAAAAGCFFEGGGGGGGFDGGFDGGHGHHHHHHHHEEGGHHHGG?EEGB#HH#HHGGEGEHHGGFGEFGHHHHGHHHHH#HHGHHHGGGGGGGGHFGHFHGGGGFHHFHHGFHHHHFEEGGGGFGGHGFDDGHFHHGCCHHGHFFGCGHFGFGCAGDFHHHFCFFFGHGGFCGCGHGBFGDA?GGHHHHHFFFGHHHHHFGFGEEAGEEE?DHHFGDF5GHG#CHFGGGGGHHHFGHFGHGHFHHHHGHGGHHFHHGHGGFHFEBGGHHFFFFGHHHGFGGGGGGGGFFFFFFFBBBAB
@SRR5527439.4 4/1
ACGTAGCGGACTACTAGGGTATCTAATCCCATTCGCTCCCCTAGCTTTCGTCTCTTAGTGTCAGTGTCGGCCCAGCAGAGTGCTTTCGCCGTTGGTGTTCTTTCCGATCTCTACGCATTTCACCGCTCCATCGGAAATTCCCTCTGCCCCTACCGTACTCAAGCTTGGTAGTTTCCACCGCCTGTCCAGGGTTAAGCCCTGGGATTTGACGGCGGACTTAAAAAGCCACCTACAGACGCTTTACGCCCAATCATTCCGGATAACGCTTGCATCCTCTGTATTACCGCGGCGGCTGGCACGCTACGT
+
BCCCCFCCCCCCGGGGGGGGGGHHHHHHHHHHHHHGGHGGGGGHHHHHHGHGHHHH###H#H#HH#HH#GGGG####HH###H#HHHGGG##G#HG#HGHHHHHHGGGG#H#HHGGGGGHHHHH#G#GG##GGGGGGH#HHH#HHFHHHGHGHH#G#GH#HFHGH#HGHGHHHHHH#HHFGGGHHHHHGGGGGHHFGGGGGEGHFGGGFGGEAHHHHGHHHHHFFGGFFHHHGGGFFFGFFFFGGGHHHGHHFEEEEGFFEGEEHHFGGEFHGHCGHHFGDEAGGGCEECCGGGBBAABBFABAA3
@SRR5527439.6 6/1
ACGTAGCGGACTACTGGGGTTTCTAATCCCATTCGCTCCCCTAGCTTTCGTCTCTTAGTGTCAGTGTCGGCCCAGCAGAGTGCTTTCGCCGTTGGTGTTCTTTCCGATCTCTACGCATTTCACCGCTCCACCGGAAATTCCCTCTGCCCCTACCGTACTCAAGCTTGGTAGTTTCCACCGCCTGTCCAGGGTTAAGCCCTGGGATTTGACGGCGGACTTAAAAAGCCACCTACAGACGCTTTACGCCCAATCATTCCGGATAACGCTTGCATCCTCTGTATTACCGCGGCGGCTGGCACGCTACGT
(seqtk-env) [vg1u23@login6002 PRJNA358488]$ ^C
(seqtk-env) [vg1u23@login6002 PRJNA358488]$ head -n 10 SRR5527439_2.fastq.gz
@SRR5527439.2 2/1
ACGTAGCGTGCCAGCCGCCGCGGTAATACAGAGGATGCAAGCGTTATCCGGAATGATTGGGCGTAAAGCGTCTGTAGGTGGCTTTTTAAGTCCGCCGTCAAATCCCAGGGCTCAACCCTGGACAGGCGGTGGAAACTACCAAGCTGGAGTACGGTAGGGGCAGAGGGAATTTCCGGTGGAGCGGTGAAATGCGTAGAGATCGGAAAGAACACCAACGGCGAAAGCACTCTGCTGGGCCGACACTGACACTGAGAGACGAAAGCTAGGGGAGCGAATGGGATTAGAAACCCGAGTAGTCCTAGTCTG
+
ABBBBCADAAAAGCFFEGGGGGGGFDGGFDGGHGHHHHHHHHEEGGHHHGG?EEGB#HH#HHGGEGEHHGGFGEFGHHHHGHHHHH#HHGHHHGGGGGGGGHFGHFHGGGGFHHFHHGFHHHHFEEGGGGFGGHGFDDGHFHHGCCHHGHFFGCGHFGFGCAGDFHHHFCFFFGHGGFCGCGHGBFGDA?GGHHHHHFFFGHHHHHFGFGEEAGEEE?DHHFGDF5GHG#CHFGGGGGHHHFGHFGHGHFHHHHGHGGHHFHHGHGGFHFEBGGHHFFFFGHHHGFGGGGGGGGFFFFFFFBBBAB
@SRR5527439.4 4/1
ACGTAGCGGACTACTAGGGTATCTAATCCCATTCGCTCCCCTAGCTTTCGTCTCTTAGTGTCAGTGTCGGCCCAGCAGAGTGCTTTCGCCGTTGGTGTTCTTTCCGATCTCTACGCATTTCACCGCTCCATCGGAAATTCCCTCTGCCCCTACCGTACTCAAGCTTGGTAGTTTCCACCGCCTGTCCAGGGTTAAGCCCTGGGATTTGACGGCGGACTTAAAAAGCCACCTACAGACGCTTTACGCCCAATCATTCCGGATAACGCTTGCATCCTCTGTATTACCGCGGCGGCTGGCACGCTACGT
+
BCCCCFCCCCCCGGGGGGGGGGHHHHHHHHHHHHHGGHGGGGGHHHHHHGHGHHHH###H#H#HH#HH#GGGG####HH###H#HHHGGG##G#HG#HGHHHHHHGGGG#H#HHGGGGGHHHHH#G#GG##GGGGGGH#HHH#HHFHHHGHGHH#G#GH#HFHGH#HGHGHHHHHH#HHFGGGHHHHHGGGGGHHFGGGGGEGHFGGGFGGEAHHHHGHHHHHFFGGFFHHHGGGFFFGFFFFGGGHHHGHHFEEEEGFFEGEEHHFGGEFHGHCGHHFGDEAGGGCEECCGGGBBAABBFABAA3
@SRR5527439.6 6/1
ACGTAGCGGACTACTGGGGTTTCTAATCCCATTCGCTCCCCTAGCTTTCGTCTCTTAGTGTCAGTGTCGGCCCAGCAGAGTGCTTTCGCCGTTGGTGTTCTTTCCGATCTCTACGCATTTCACCGCTCCACCGGAAATTCCCTCTGCCCCTACCGTACTCAAGCTTGGTAGTTTCCACCGCCTGTCCAGGGTTAAGCCCTGGGATTTGACGGCGGACTTAAAAAGCCACCTACAGACGCTTTACGCCCAATCATTCCGGATAACGCTTGCATCCTCTGTATTACCGCGGCGGCTGGCACGCTACGT


use the delete function again: 
find . -maxdepth 1 -type f -name "*.fastq.gz" ! -name "*_1.fastq.gz" -delete
check again: only _1 should be there

considering studyID PRJNA529046: 
the fastq files without ending are single end. only _2 reverse reads were deleted.



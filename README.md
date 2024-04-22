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


#Strategy: Use the edirect plugin for retrieving metadata, download the csv files (go into your scratch folder and use the download function) and store them also in OneDrive, get the SRP Number from the metadata files (study number) and retrieve the sequence data with pysradb. Store everything together in a folder. Check if the download worked and the file is not empty.

#end qiime2 etc.
conda deactivate

#check the environment for plugins
conda info --env

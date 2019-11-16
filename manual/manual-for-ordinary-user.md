---
description: Manual for people without programming background.
---

# Manual - for Ordinary User

* **Please put the script \(Assignment02.py\) in a empty directory.**
* **Switch directory to the directory where the script is located.**
* **Use Python3 to run the script:**

```bash
$ mkdir B153665
$ cd ./B153665
$ cp <the script you have downloaded> ./
$ python3 ./Assignment02.py
```

* **You will see the prompt.**
* At this step, once you have prepared the dataset, input "1" to continue: 

```yaml
#=======================================
# Project: BPSM Assignment02
# Version: 1.0
# Author: B153665-2019
# Github: https://github.com/B153665-2019
#=======================================

For analysis, you need to input: 
#======================#
# Taxonomic Group Name #
# Protein Family Name  #
#======================#

—————————————————Make Your Choice—————————————————
1. I have already prepared the information you want, let's start!
2. Sorry, I will consider it again.
——————————————————————————————————————————————————

input 1~2 to select: 1
```

* After input "1", you will see the prompt which let you input taxonomic group name. Just input the name you have prepared. As an example, here we input birds:
* **Please make sure to SPELL correctly!**

```bash
#====================Prompt====================
Please input the taxonomic grou name.
Please be sure to spell correctly!
#=====================End======================

Please input the taxonomic group you want:birds
```

* After inputting the taxonomic group name, if input correct, you will see result\(s\) with index. 
* In this case, only one result of taxonomic group found \(birds\) , so we just input "1" :

```bash
The following are the result(s) found by keyword that you input.
#============================
1. Aves
    (birds), class, birds
#============================
Please input the index of the taxonomic group that you want.
Index:1
```

* After input the index of taxonomic group, then input the protein family name. As an example, we input   "glucose-6-phosphatase" here**:**

```bash
#==============================Prompt==============================
# You have already chosen the taxonomic group.
# Then, you need to input the title of Protein Family that you want.
# Please make sure the spelling is correct
#================================End===============================

Input the protein family :glucose-6-phosphatase
```

* After input the protein family name, you need to input the query criteria. NOT PARTIAL means you will not get the sequence incomplete, and NOT PREDICTED means you will only get the sequences which included in transcriptome. Follow your need to choose "Yes" or "No". As an example, we choose \[NOT PARTIAL\] yes, and \[NOT PREDICTED\] no:

```bash
Whether add NOT PARTIAL to query?
1. Yes
2. No
Please input 1 or 2 :1
Whether add NOT PREDICTED to query?
1. Yes
2. No
Please input 1 or 2 :2
Fetching sequences, please wait..
```

* After choosing, the sequences will be fetched soon. And you will see new prompt, the txid of the taxonomic group you have chosen, protein family keyword you have input, total sequences found, and the location of the FASTA format sequence file.
* Then, you will have another choice. To ensure the reliability of the analysis later, we will help you to find out 250 sequences with the highest similarity. Of course, you can choose whether to find or not.
* As an example, we choose "1" here, to get 250 most similar sequences:

```yaml
The information you have chosen: 
#=============================================================
# txid: 8782
# Protein Family Keyword: glucose-6-phosphatase
# Total Seuences Found: 371
# FASTA format sequences are stored in <./protein_family.fasta>
#=============================================================


—————————————————————————————Make Your Choice————————————————————————
1. Correct info, get most similar 250 sequences for conservation analysis and some other steps!
2. Although the protein sequences are more than 250, I need all of them for conservation analysis!
3. Some problem on my information, I want to reinput that!
4. I will consider it again, I want to quit!
—————————————————————————————————————————————————————————————————————

input 1~3 to select: 1
```

* The script will then go through "Looking for 250 most similar sequences", "create conservation plot" and "create distance matrix". After that, the location of result files saved will be print on screen.
* After this step, the script will ask you whether to go to next step. The next step is scan the Prosite database with the sequence we have fetched \(If you choose get 250 most similar sequences, this step will only use those 250 sequences\).
* Here we input "1" to continue.

```markup
Finding the most similar 250 sequences, please wait...


Building a new DB, current time: 11/14/2019 22:56:58
New DB name:   /localdisk/home/s1940724/Assignment02-test/REF
New DB title:  protein_family.fasta
Sequence type: Protein
Keep MBits: T
Maximum file size: 1000000000B
Adding sequences from FASTA; added 371 sequences in 0.0202169 seconds.
Conservation plot processing, please wait...
Plot conservation of a sequence alignment
Created plotcon.svg
Conservation Analysis Finished:

Five sequences have highest conservation level are:
Accession ID	Level_of_Conservation
XP_009323684.1	91.13662111599999
XP_010190172.1	91.10564185600002
XP_010565519.1	91.12379761199992
XP_011577872.1	91.12379761199992
XP_029874515.1	91.12379761199992

#=============================================')
# Conservation Plot saved as: <./plotcon.svg>')
# Alignment result saved as: <./alignment.fasta>')
# Level of Conservation : <./percent_identity.txt>')
#=============================================')
Here, we regard mean percent identity with other sequences
of each sequence as its level of conservation.

Continue to scan of motif?
Input 1 to continue, 2 to exit: 1
```

* After this process, we can get a table with sequences' accession ID and the name of motifs on those sequences. The results will show on our screen and the file will be saved, the location of the result file will be shown on screen.
* Apart from these main functions, this script also has some optional functions. We can input "1" here to continue to the optional functions:
* \(NOTE: If you choose get 250 most similar sequences, the next all optional functions will use those 250 sequences.\)

```yaml
Processing, please wait...
Protein Sequence XP_009903782.1 have motif(s): TNF_1
Protein Sequence XP_009903783.1 have motif(s): TNF_1
Protein Sequence XP_010199970.1 have motif(s): TNF_1
Protein Sequence XP_013055223.1 have motif(s): AMIDATION
Protein Sequence XP_008936739.1 have motif(s): AMIDATION
Protein Sequence XP_009553670.1 have motif(s): AMIDATION
Protein Sequence XP_009892089.1 have motif(s): AMIDATION
Protein Sequence XP_009460192.1 have motif(s): AMIDATION
Protein Sequence XP_009982315.1 have motif(s): AMIDATION
Protein Sequence XP_005439497.2 have motif(s): AMIDATION
Protein Sequence XP_009646606.1 have motif(s): AMIDATION
Protein Sequence XP_010207957.1 have motif(s): AMIDATION
Protein Sequence XP_010207956.1 have motif(s): AMIDATION
Protein Sequence XP_009957477.1 have motif(s): AMIDATION
Protein Sequence XP_009646599.1 have motif(s): AMIDATION
Protein Sequence XP_013806897.1 have motif(s): AMIDATION
Protein Sequence XP_009281191.1 have motif(s): AMIDATION
Protein Sequence XP_027301312.1 have motif(s): AMIDATION
Protein Sequence XP_005238894.2 have motif(s): AMIDATION
#=================================
# Motif information is saved in 
#         <./motifs.txt>
#      All main steps done!
#=================================
This script has some other optional functions, continue?
1. Yes.
2. No.
Please input 1 or 2 to choose: 1
```

* There are 3 optional functions you can choose, according to your need and choose you want, all the location of output files will show on the screen, you can follow the location to find them.

```bash
————————————Optional Functions————————————
1. Create a Consensus Sequence
2. Create a Hydropathy Plot
3. Create a Charge Plot
——————————————————————————————————————————
Please input 1 to 3 to choose: 
```

```yaml
Create a consensus sequence from a multiple alignment
#=============================================
#        Consensus Sequence saved as: 
#        <./consensus_sequence.fasta>
#=============================================


Draw Kyte-Doolittle hydropathy plot for a protein alignment
Created Hydropathy.svg
#============================================
#  Kyte-Doolittle hydropathy Plot saved as: 
#          <./Hydropathy.svg>
#============================================


Draw a protein charge plot
Created charge.svg
#==================================
#        Charge Plot saved as: 
#         <./charge.svg>
#==================================

```


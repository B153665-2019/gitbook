---
description: Manual for people without programming background.
---

# Manual - for Ordinary User

* **Please put the script \(Assignment02.py\) in a empty directory.**
* **Switch directory to the directory where the script is located。**
* **Use Python3 to run the script:**

```bash
$ python3 ./Assignment02.py
```

* **You will see following prompt:**
* At this step, once you have prepared the dataset, input "1" to continue: 

```bash
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

```bash
#====================Prompt====================
Please input the taxonomic grou name.
Please be sure to spell correctly!
#=====================End======================

Please input the taxonomic group you want:birds
```

* After inputting the taxonomic name, if input correct, you will see the result\(s\) found with index. In this case, only one result of taxonomic group found, so we just input "1" :

```bash
The following are the result(s) found by keyword that you input.
1. Aves
    (birds), class, birds
Please input the index of the taxonomic group that you want.
Index:1
```

* After input the index of taxonomic group, then input the protein family name. As an example, we input   glucose-6-phosphatase here**:**

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
* Then, you will have another choice. To ensure the reliability of the analysis later, we will screen out 250 sequences with the highest similarity. Of course, you can choose whether to filter or not.
* As an example, we choose "1" here:

```bash
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

input 1~3 to select: 

```

\*\*\*\*


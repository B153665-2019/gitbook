---
description: Manual for people with programming background.
---

# Manual - for Professional User

## Flow Chart:

![](../.gitbook/assets/untitled-diagram-5.png)

* For the Python package, the following packages we imported.

```python
import os #For using edirect, EMBOSS, and Clustal Omega
import re #Regular Expression
import xml.etree.cElementTree as et #XML file processing
```

* Before we start, we ask whether user have already prepared the information need to input, because the correct information make the script run smoothly:

```python
#Make sure the user is ready to start this script
def start_confirmation():
	print("For analysis, you need to input: ")
	print("#======================#")
	print("# Taxonomic Group Name #")
	print("#  Protein Family Name #")
	print("#======================#")
	print("")
	print("—————————————————Make Your Choice—————————————————")
	print("\033[0;32m1.\033[0m All information prepared, let's start!")
	print("\033[0;32m2.\033[0m Sorry, I will consider it again.")
	print("——————————————————————————————————————————————————")
	print("")
	choice1 = input("input 1~2 to select: ")
	while re.match('^[1-2]$',str(choice1)) is None:
		print("\033[0;31m%s\033[0m" % "Invalid Input, please input that again!")
		choice1 = input("Input 1~2 to select: ")
	if choice1 == '1':
		input_info()
	elif choice1 == '2':
		print("\033[0;31m%s\033[0m" % "Bye!")
		exit()
```

* If user input "1" in last step, the function "input\_info\(\)" will be called. Firstly, the "input\_info\(\)" will give user some prompt, and let user to input the name of taxonomic group, and assign the input string to a variable. For avoid unnecessary trouble, lower all character and replace all space to +. \(In NCBI query, "+" represent space.

```python
raw_taxonomic_group = input("Please input the taxonomic group you want:")
raw_taxonomic_group = raw_taxonomic_group.lower()
raw_taxonomic_group = raw_taxonomic_group.replace(' ','+')
```

* After process the characters, use esearch and efetch to query the NCBI taxonomy database. the result include the taxonomic group name and index. Read all these information in a variable:

```python
taxonomic_group_txt=os.popen("esearch -db taxonomy -query " + raw_taxonomic_group + " | efetch -format txt").read().split('\n')
```

* Use which loop to determine whether the result of query is null \(the variable is null\). If so, means no result found in query, let user to re-input the taxonomy group name until the variable is not null, jump out of loop.

```python
while taxonomic_group_txt[0]=='':
    ...
    ...
    taxonomic_group_txt=os.popen("esearch -db taxonomy -query " + raw_taxonomic_group + " | efetch -format txt").read().split('\n')
```

* The result can be one taxonomic group or more than one. And the each group occupy 2 lines of result:

```python
1. Aves
    (birds), class, birds
```

* So, we could use this loop to print out the result and count how many group found \(t\):

```python
	t=0
	for line in range(round(len(taxonomic_group_txt)/2)):
		print("\033[0;32m%s\033[0m" % taxonomic_group_txt[t])
		print(taxonomic_group_txt[t+1])
		t+=2
	print("#============================")
```

* Let user to input the index and we use efetch again to get the txid, the index that user input represent a txid, the txid is for following step. If the index input is "1", 1-1=0, txid\[0\] is the corresponding txid

```python
	input_index = int(input_index)
	txid = os.popen("esearch -db taxonomy -query " + raw_taxonomic_group + " | efetch").read().split('\n')
	txid = txid[int(input_index)-1]
```

* After confirm the taxonomic, let user input Protein Family name. Subsequently, let user choose whether add \[NOT PARTIAL\] and \[NOT PREDICTED\] as query criteria. \[NOT PARTIAL\] means filter the partial sequence, and \[NOT PREDICTED\] means filter the predicted sequence. The error trap here use the regular expression to match.

```python
	#input the protein family name
	protein_family = input("Input the protein family :")
	#replace space with +, because space sometimes cause problem.
	protein_family = protein_family.replace(' ','+')
	#lower the letter, for inputting in the query of esearch
	protein_family = protein_family.lower()

	#ask user whether to add NOT PARTIAL
	print('Whether add [NOT PARTIAL] to query?')
	print('1. Yes')
	print('2. No')
	input_not_partial= input("Please input 1 or 2 :")
	while re.match('^[1-2]$',str(input_not_partial)) is None:
		print('Invalid Input, please input only 1 or 2 :')
		input_not_partial = input("Input the index :")

	#ask user whether to add NOT PREDICTED
	print('Whether add [NOT PREDICTED] to query?')
	print('1. Yes')
	print('2. No')
	input_not_predicted= input("Please input 1 or 2 :")
	while re.match('^[1-2]$',str(input_not_predicted)) is None:
		print('Invalid Input, please input only 1 or 2 :')
		input_not_predicted = input("Input the index :")

	#assign variables of NOT PARTIAL and NOT PREDICTED for next step
	if input_not_partial == '1':
		not_partial = ' NOT PARTIAL'
	else:
		not_partial = ''

	if input_not_predicted == '1':
		not_predicted = ' NOT PREDICTED'
	else:
		not_predicted = ''
```

* Query the NCBI database with txid, Protein Family Name, the some variable, at this step, don't directly fetch the sequence but get the information of query result, if there are too many sequences found, fetch the sequence directly take too much time.
* Here, the query result format is XML, we use et package that we have imported to get the count of sequence:

```python
	os.system("esearch -db protein -query \"txid" + txid + "[Organism]+" + protein_family + "[TI]" + not_partial + not_predicted + "\" >./esearch.xml")
	parser = et.parse("esearch.xml")
	root = parser.getroot()
	esearch_count = root[3].text
```

* Determine the count of sequence, if it's more than 10000, don't fetch sequences, ask user whether to continue. 
* If the count is "0", don't try to fetch the sequence, let user to re-input.
* If the count is from 250 to 10000, fetch sequences, and ask user whether to get 250 most similar sequences.
* If the count is more than 0 and less than 250 \(else\), fetch the sequences, ask user whether to continue.

```python
if int(esearch_count) > 10000:
    ...
    ...
    input_info()
elif int(esearch_count) == 0:
    input_info()

if int(esearch_count) > 250:
    ...
    ...
else:
    ...
```

* If user choose to get 250 most similar sequence, call function "get\_250\_sequence\(\)".
* The "get\_250\_sequence\(\)" function has these steps to get 250 most similar sequences.

1.Using clustalo's distmat to align all of the sequences in pairs, and get a distance matrix. the row of the matrix represent the distance score between one sequence with other sequences. And the less the score, the more closer. After getting the distance matrix, open read and split that in lines. Using regular expression to substitute more than one space to only one space, which is for split in next step. Then use for loop to calculate the sum of score.

```python
os.system("clustalo -i " + protein_family_250 + " --distmat-out=protein.mat --threads=10 --full --force -o ./alignment_ge_250.fasta")
distmat=open('protein.mat').read().split('\n')
	#create a dictionary, keys are the ID of protein, and values are sum of score.
	score_dict={}
	for line in distmat:
		line=re.sub(' +', ' ', line)  #Some line with more than one space between fields
		num_with_name=line.split(' ')
		num_only_num=num_with_name[1:]

		#sum the score
		total_score=0
		for n in num_only_num:
			total_score+=float(n)
		score_dict[num_with_name[0]]=total_score
```

2. For each sequence, need to sum all of the row. Find out the sequence with lowest sum of score, which means lowest sum of distance.

3. Use this sequence to query other sequences. Other sequences have been set as a blast database.

4. Use blastp to align the database we have built.

5. Find out 250 sequences with higher score.




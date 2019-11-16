---
description: Manual for people with programming background.
---

# Manual - for Professional User

## Flow Chart:

![](../.gitbook/assets/untitled-diagram-5.png)

* For the Python package, the following packages we imported:

```python
import os #For using edirect, EMBOSS, and Clustal Omega
import re #Regular Expression
import xml.etree.cElementTree as et #XML file processing
```

* Before we start, we ask whether user have already prepared the information need to input, because the correct information make the script run smoothly. The input should be 1 or 2. Some invalid input will be match by regular expression and jump into the while loop to let user input again. The following steps also use this kind of error trap to avoid invalid input:

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

* If user input "1" in last step, the function "input\_info\(\)" will be called. Firstly, the "input\_info\(\)" will give user some prompt, and let user to input the name of taxonomic group, and assign the input string to a variable. For avoid unnecessary trouble, lower all character and replace all space to +. \(In NCBI query, "+" represent space.\)

```python
raw_taxonomic_group = input("Please input the taxonomic group you want:")
raw_taxonomic_group = raw_taxonomic_group.lower()
raw_taxonomic_group = raw_taxonomic_group.replace(' ','+')
```

* After process the characters, use esearch and efetch to query the NCBI taxonomy database. the result include the taxonomic group name and index. Read all these information in a variable:

```python
taxonomic_group_txt=os.popen("esearch -db taxonomy -query " + raw_taxonomic_group + " | efetch -format txt").read().split('\n')
```

* Use while loop to determine whether the result of query is null \(the variable is null\). If so, means no result found in query, let user to re-input the taxonomy group name until the variable is not null, jump out of loop.

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

2. Find out the sequence with lowest sum of score, which means lowest sum of distance. Firstly we sort the sum of score, and get the ID of sequence with the lowest score, and fetch this sequence in a new file by using efetch:

```python
score_dict=sorted(score_dict.items(), key=lambda d:d[1], reverse = False)
ref_seq=score_dict[0][0]
os.system("efetch -db protein -id " + ref_seq + " -format fasta>./ref_seq.fasta")
```

3. Use this sequence to query other sequences. Other sequences have been set as a blast database:

```python
#set blast database
os.system("makeblastdb -in " + protein_family_250 + " -dbtype prot -out REF")

#query the blast database with lowest score sequence we found
os.system("blastp -db REF -query ./ref_seq.fasta -outfmt 7 > blastoutput.out")
```

4. Find out 250 sequences with higher score. Open the blastp result file in lines, and assign to a variable "blast\_result". Use several for loop to get useful lines \(the lines start with "\#" don't have info we want, so we discard those lines\) and sort the result by bit score, bit score is in last field so we get "fields\[-1\]". After that, the sorted info is in a list, that variable called "seq\_id".

```python
	blast_result=open('blastoutput.out').read().split('\n')
	#discard last null line.
	blast_result=blast_result[0:-1]
	#delete the output file becuse it is already in varible
	os.system("rm ./blastoutput.out")

	#varible blast_result_1 means the list contain the line without '#'
	#because the line with '#' don't have information of identity.
	blast_result_1=[]
	for line in blast_result:
		if line[0] != '#':
			blast_result_1.append(line)

	#Set a dictionary, the ID of protein as keys, and sore as values
	seq_id={}
	for line1 in blast_result_1:
		fields=line1.split('\t')
		seq_id[fields[1]]=fields[-1]

	#convert the score from string to int
	for n in seq_id:
		seq_id[n]=float(seq_id[n])

	#sort the score
	seq_id=sorted(seq_id.items(), key=lambda d:d[1], reverse = True)
```

5. Get the top 250 of variable "seq\_id", which are the sequence IDs which are most similar:

NOTE: If get no more than 250 sequences, or  0 sequence. Which means the sequences fetched have problem \(A partial sequence is regarded as reference sequence or other problems\). So, here let user to re-input the query information and call the "input\_info\(\)" function again.

```python
if len(seq_id) < 250:
		print("\033[0;31m%s\033[0m" % "#==============================WARNING==============================")
		print("# We could not find the 250 most similar sequences")
		print("# This is because the protein family does not meet the standard.")
		print("# It is highly recommended that you add [NOT PARTIAL], to your query and try again")
		print("\033[0;31m%s\033[0m" % "#================================End================================")
		os.system("rm ./protein_family.fasta")
		input_info()
		
seq_id=seq_id[0:250]
```

6. Fetch those 250 sequences to a new file by using efetch. Firstly join the list with comma, because fetching multiple sequences on efetch need to use comma to separate.

```python
seq_250_name=[]
for prot_name in seq_id:
	seq_250_name.append(prot_name[0])
	seq_250_name=','.join(seq_250_name)
	#fetch these 250 sequences again to a new file
	os.system("efetch -db protein -id " + seq_250_name + " -format fasta>./pf_new.fasta")
```

* After getting the 250 most similar sequence, the next step of script is analyze the level of conservation. And the function for this step is "conservation\_plot\(\)". This step, firstly, use Clustal Omega to get a alignment output and percent-id matrix. The alignment output is for the conservation plot, and the percent-id matrix is used to calculate the level of conservation \(Here, we compare sequences in pairs and calculate the mean value of percent identity and regard that as level conservation. \(This step to calculate the mean of percent\_identity is 

```python
os.system("clustalo -i " + conver_input_fasta + " --distmat-out=protein.mat --percent-id --threads=10 --full --force -o ./alignment.fasta")

#open and read the .mat file in python as list, and separate in lines.
distmat=open('protein.mat').read().split('\n')

#create a dictionary, keys are the ID of protein, and values are sum of score.
score_dict={}
for line in distmat:
	line=re.sub(' +', ' ', line)  #Some line with more than one space between fields
	num_with_name=line.split(' ')
	num_only_num=num_with_name[1:]

	#convert string to float
	for a in range(len(num_only_num)):
		num_only_num[a] = float(num_only_num[a])

	#calculate the mean of identity
	mean=sum(num_only_num)/len(num_only_num)
	score_dict[num_with_name[0]]=mean

#open and write the percent_identity (here regard percent_identity as level of conservation) in lines
open('percent_identity.txt','w').write('Accession ID\tLevel_of_Conservation\n')
for n in score_dict:
	open('percent_identity.txt','a').write(n + "\t" + str(score_dict[n]) + "\n")
```

* After do the conservation analysis, next step is scan the prosite database with the sequence we have gotten \(If choose 250 most similar sequence, here use those 250 sequences\).
* Because the EMBOSS tool "patmatmotifs" we use only can scan one sequence each time. So:

1.Split the fasta file which with many sequences: open each sequence in loop as a "cache" fasta file.

2.Use that "cache" fasta sequence to scan the prosite db, in the mean time, get the motif name.

3.After one sequence finished, open another sequence and repeat again and again, for more detail is described in comments:

```python
	#open fasta file with many sequences
	all_seq_fasta = open(protein_fasta_motif).read().split('\n')
	#discard the last null value
	all_seq_fasta = all_seq_fasta[0:-1]

	#define a null list for saving single fasta sequence
	seq_list_fasta = []
	#defin a null dic for saving protein id as key, motif name as value.
	motifs_dic={}
	for a in range(len(all_seq_fasta)):
		seq_list_fasta.append(all_seq_fasta[a])

		#determine if it is the last line of fasta file(with many sequences)
		if a == len(all_seq_fasta)-1:
			#join the list, and this list contain only one sequence
			seq_str = '\n'.join(seq_list_fasta)
			#write this list to a single sequence fasta file
			open('cache.fasta','w').write(seq_str)
			#use patmatmotifs to scan the prosite db with single sequence
			os.system("patmatmotifs ./cache.fasta -outfile ./protein.patmatmotifs -auto")

			#open the output of patmatmotifs
			patmatmotifs = open('protein.patmatmotifs').read().split('\n')
			patmatmotifs = patmatmotifs[0:-1]
			for line_motifs in patmatmotifs:
				#determine whether this line have motif name
				if line_motifs[0:8] == 'Motif = ':
					#determine whether the key exist, if exist, append, if not, assign that key
					if patmatmotifs[13].split(' ')[2] in motifs_dic.keys():
						motifs_dic[patmatmotifs[13].split(' ')[2]] = motifs_dic[patmatmotifs[13].split(' ')[2]]+','+line_motifs.split(' ')[2]
					else:
						motifs_dic[patmatmotifs[13].split(' ')[2]] = line_motifs.split(' ')[2]
			break
		#determine if it is the last line of single sequence (in the fasta file with many sequences)
		if all_seq_fasta[a+1][0] == '>':
			#join the list, and this list contain only one sequence
			seq_str = '\n'.join(seq_list_fasta)
			#write this list to a single sequence fasta file
			open('cache.fasta','w').write(seq_str)
			#use patmatmotifs to scan the prosite db with single sequence
			os.system("patmatmotifs ./cache.fasta -outfile ./protein.patmatmotifs -auto")
			#open the output of patmatmotifs
			patmatmotifs = open('protein.patmatmotifs').read().split('\n')
			patmatmotifs = patmatmotifs[0:-1]
			for line_motifs in patmatmotifs:
				#determine whether this line have motif name
				if line_motifs[0:8] == 'Motif = ':
					#determine whether the key exist, if exist, append, if not, assign that key
					if patmatmotifs[13].split(' ')[2] in motifs_dic.keys():
						motifs_dic[patmatmotifs[13].split(' ')[2]] = motifs_dic[patmatmotifs[13].split(' ')[2]]+','+line_motifs.split(' ')[2]
					else:
						motifs_dic[patmatmotifs[13].split(' ')[2]] = line_motifs.split(' ')[2]
			#one sequence finished, reset the list for next sequence
			seq_list_fasta = []
```

* After the dictionary is set, the keys are sequence IDs, and the values are name of Motifs. But some sequences with the motifs have the same name in different position, so we need to set the same name. After that, print the result on screen and save in a file called "motifs.txt".

```python
	#set the motif name, discard the sequence with more than one motif with the same name
	final_motifs=[]
	for x in motifs_dic:
		n = motifs_dic[x]
		n = n.split(',')
		n = set(n)
		n=','.join(n)
		#print out the result
		print("Protein Sequence " + x + " have motif(s): " + n)
		final_motifs.append(x+'\t'+n)
	#write the result to a file
	open('motifs.txt','w').write('\n'.join(final_motifs))
```

* After scan of motif finished, then go to optional step. Optional step include the using of EMBOSS tools. Just use "cons", "pepwindowall" and "charge" can get the consensus sequence, hydropathy plot and charge plot respectively.

```python
os.system("cons -sequence " + cs_input + " -outseq ./consensus_sequence.fasta")

os.system("pepwindowall " + hydropathy_input + " -graph svg -gxtitle \'Amino Acid Residue\' -goutfile Hydropathy")

os.system("charge -seqall " + charge_input + " -graph svg -plot")
```

* All steps done.






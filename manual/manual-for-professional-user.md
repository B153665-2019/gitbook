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

* dd

```python
def input_info():
	print("")
	#print the input guidance
	print("#====================Prompt====================")
	print("\033[0;32m%s\033[0m" % "Please input the taxonomic grou name.")
	print("\033[0;31m%s\033[0m" % "Please be sure to spell correctly!")
	print("#=====================End======================")
	print("")
	#input the taxonomic group name that user want
	raw_taxonomic_group = input("Please input the taxonomic group you want:")
	#lower the letter, for inputting in the query of esearch
	raw_taxonomic_group = raw_taxonomic_group.lower()
	#replace space with +, because the query of esearch can't include space.
	raw_taxonomic_group = raw_taxonomic_group.replace(' ','+')
	#open, read, and split the output of esearch and efetch.
	taxonomic_group_txt=os.popen("esearch -db taxonomy -query " + raw_taxonomic_group + " | efetch -format txt").read().split('\n')

	#if no result found, let user to input again.
	while taxonomic_group_txt[0]=='':
		#tell user no results found, and a guidance for using taxonomy to know the correct name
		print("")
		print("\033[0;31m%s\033[0m" % "#=====================WARN=====================")
		print("# No result found, please input again.")
		print("# If you are unclear about the name of taxonomic group, please see:")
		print(r'# https://www.ncbi.nlm.nih.gov/taxonomy')
		print("\033[0;31m%s\033[0m" % "#=====================End======================")
		print("")
		#input the taxonomic group name that user want
		raw_taxonomic_group = input("Please input again! :")
		#lower the letter, for inputting in the query of esearch
		raw_taxonomic_group = raw_taxonomic_group.lower()
		#replace space with +, because the query of esearch can't include space.
		raw_taxonomic_group = raw_taxonomic_group.replace(' ','+')
		#open, read, and split the output of esearch and efetch.
		taxonomic_group_txt=os.popen("esearch -db taxonomy -query " + raw_taxonomic_group + " | efetch -format txt").read().split('\n')

	#delete the last null value
	taxonomic_group_txt=taxonomic_group_txt[0:-1]
	#list all of the taxonomic group result(s) found, and let users to choose what they want 
	print("#============================")
	print("\033[0;31m%s\033[0m" % "The following are the result(s) found by keyword that you input.")

	t=0
	for line in range(round(len(taxonomic_group_txt)/2)):
		print("\033[0;32m%s\033[0m" % taxonomic_group_txt[t])
		print(taxonomic_group_txt[t+1])
		t+=2
	print("#============================")
	#let user to input the index of the taxonomic group
	print("\033[0;31m%s\033[0m" % "Please input the index of the taxonomic group that you want.")
	input_index = input("Index:")

	#if the input is not correct, input that again
	while re.match('^[1-'+str(round(len(taxonomic_group_txt)/2))+']$',str(input_index)) is None:
		print('Invalid Input, please input that again!')
		input_index = input("Input the index :")

	#get the txid of the taxonomic group for next step
	input_index = int(input_index)
	txid = os.popen("esearch -db taxonomy -query " + raw_taxonomic_group + " | efetch").read().split('\n')
	txid = txid[int(input_index)-1]

	#next step, let user to input the name of protein family
	print("")
	print("#==============================Prompt==============================")
	print('# You have already chosen the taxonomic group.')
	print('# Then, you need to input the title of Protein Family that you want.')
	print('# Please make sure the spelling is correct')
	print("#================================End===============================")
	print("")
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
	#query the keyword that the user input, get the count of sequence in XML file	
	os.system("esearch -db protein -query \"txid" + txid + "[Organism]+" + protein_family + "[TI]" + not_partial + not_predicted + "\" >./esearch.xml")
	parser = et.parse("esearch.xml")
	root = parser.getroot()
	esearch_count = root[3].text
	#delete esearch.xml because the main information is in varible
	os.system("rm ./esearch.xml")
	#if the number of sequences are larger than 10000, let user to decide whether to continue or not
	if int(esearch_count) > 10000:
		print("")
		print("\033[0;31m%s\033[0m" % "#==============================WARNING==============================")
		print("# More than 10000 sequences found.")
		print("# Totally Found " + esearch_count + " sequences.")
		print("# It is highly recommended that you re-enter the protein family keyword!")
		print("\033[0;31m%s\033[0m" % "#================================End================================")
		print("")
		print("1. Contunue! (Not Recommended)")
		print("2. Re-input information!")
		print("3. Quit!")		
		large_sequence_choice = input("input 1~3 to select: ")
		while re.match('^[1-3]$',str(large_sequence_choice)) is None:
			print('Invalid Input, please input that again!')
			large_sequence_choice = input("input 1~3 to select: ")
		if large_sequence_choice == '2':
			input_info()
		elif large_sequence_choice == '3':
			print("\033[0;31m%s\033[0m" % "Bye!")
			exit()
	elif int(esearch_count) == 0:
		print("\033[0;31m%s\033[0m" % "No result found, please check your input or change search criteria and re-input!")
		input_info()

	print("Fetching sequences, please wait...")
	#because can't use Biopython, need to use os.system to use the esearch and efetch to get proteins' fasta files
	os.system("esearch -db protein -query \"txid" + txid + "[Organism]+" + protein_family + "[TI]" + not_partial + not_predicted + "\"" + " | efetch -format fasta>./protein_family.fasta")
	#get the file size for judging whether the result is correct or not

	#tell user the whole information
	print("\033[0;32m%s\033[0m" % "The information you have chosen: ")
	print('#=============================================================')
	print('# txid: ' + txid)
	print('# Protein Family Keyword: ' + protein_family.replace('+',' '))
	print('# Total Seuences Found: ' + str(esearch_count))
	print('# FASTA format sequences are stored in <./protein_family.fasta>')
	print('#=============================================================')
	print('')
	#ask user whether to go to the next step, if so, go to which step
	if int(esearch_count) > 250:
		print("")
		print("—————————————————————————————Make Your Choice————————————————————————")
		print("\033[0;32m1.\033[0m Correct info, get most similar 250 sequences for conservation analysis and some other steps!")
		print("\033[0;32m2.\033[0m Although the protein sequences are more than 250, I need all of them for following steps!")
		print("\033[0;32m3.\033[0m Some problem on my information, I want to re-input that!")
		print("\033[0;32m4.\033[0m I will consider it again, I want to quit!")
		print("—————————————————————————————————————————————————————————————————————")
		print("")

		choice2 = input("input 1~3 to select: ")
		while re.match('^[1-3]$',str(choice2)) is None:
			print('Invalid Input, please input that again!')
			choice2 = input("input 1~2 to select: ")
		if choice2 == '1':
			get_250_sequence()
		elif choice2 == '2':
			#default input file name of next step is <pf_new.fasta>, so need to copy protein_family.fast
			os.system("cp ./protein_family.fasta ./pf_new.fasta")
			conservation_plot()
		elif choice2 =='3':
                        os.system("rm ./protein_family.fasta")
                        input_info()
		elif choice2 == '4':
			print("\033[0;31m%s\033[0m" % "Bye!")
			os.system("rm ./protein_family.fasta")
			exit()

	else:
		print("")
		print("————————————————————————————————Make Your Choice—————————————————————————————————————————")
		print("\033[0;32m1.\033[0m I am satisfied with this info, continue to conservation analysis!")
		print("\033[0;32m2.\033[0m Some problem on my information, I want to re-input that!")
		print("\033[0;32m3.\033[0m I will consider it again, I want to quit!")
		print("—————————————————————————————————————————————————————————————————————————————————————————")
		print("")
		choice2 = input("input 1~2 to select: ")
		while re.match('^[1-2]$',str(choice2)) is None:
			print('Invalid Input, please input that again!')
			choice2 = input("input 1~2 to select: ")
		if choice2 == '1':
			os.system("cp ./protein_family.fasta ./pf_new.fasta")
			conservation_plot()
		elif choice2 == '2':
			os.system("rm ./protein_family.fasta")
			input_info()
		elif choice2 == '3':
			print("\033[0;31m%s\033[0m" % "Bye!")
			exit()
```


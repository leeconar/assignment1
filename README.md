# Repository for work on Assignment 1
   
##File Inspection:  
File sizes:  (du -h command)  
Fang- 11 megabytes  
Snps- 84 kilobytes

Lines:   (wc command)  
Fang-2783 lines  
Snps- 984 lines

Columns:   (awk –F “\t” ‘{print NF; exit}’ filename.txt)  
Fang-986  
Snps-15

Words:  (wc command)  
Fang- 2744038  
Snps- 13198

Characters:   (wc command)  
Fang-11051939  
SNPs-82763

File types:  (file command)  
Fang- ASCII text, with very long lines  
SNPs- ASCII English text
  
##File Processing  
Note: unless otherwise noted, between each step I used head –n 5 to determine the layout of my files. Also, most steps are performed for both a teosinte file and a maize file, but I have only listed the steps for the maize file, as the only change occurring is the replacement of “maize” with “teosinte.”    

1.	Used grep to move only headers of fang_et_al file to new files:  
grep “Sample_ID” fang\_et\_al\_genotypes.txt >maize\_total.txt 

2.	Used grep to add the contents of each file using the provided codes for maize and teosinte:  
grep –E “ZMMIL|ZMMLR|ZMMMR” fang\_et\_al\_genotypes.txt >>maize\_total.txt

3.	Use the transpose.awk script provided to transpose both files:  
awk –f transpose.awk fang\_et\_al\_genotpyes.txt >maize\_transposed.txt

4.	Sort both corn variety files, and the snp_position.txt file using the following:  
sort –k1 maize\_transposed.txt >sorted\_transposed\_maize.txt
sort –k1 snp\_position.txt >sorted\_SNPs

5.	Joined SNPs file and corn variety files as follows:  
join –t $’\t’ -1 1 -2 1 sorted\_SNPs\ sorted\_transposed\_maize.txt >joined\_maize.txt

6.	To check that this join worked, I looked at the number of columns on my new file, as well as the word count using the wc command and:  
awk –F “\t” ‘{print NF; exit}’ joined\_maize.txt  
These numbers looked accurate based on the number of columns in the previous step. 

7.	Reordered columns such that the first column is SNP_ID, the second column is chromosome, and the third is position:  
cut –f1,3,4 joined\_maize.txt >first3\_maize\_hyph.txt  
cut –f2,5- joined\_maize.txt >remainder\_maize.txt  
paste first3\_maize.txt remainder\_maize.txt >final\_maize.txt  

8.	Substituted “-“ for “?” in each of the final files:  
sed 's/?/-/g' final\_maize.txt > hyph\_maize\_final.txt

9.	Split each of the files (2 corn varieties with “?”, and tow with “-“) into 10 files (1 for each chromosome) with:  
for i in {1..10}; do awk ‘$2== ‘$i’’ final\_maize.txt > chr“$i”\_maize\_ques.txt;done  
Replaced certain parts in order for this to work with each of my four files (e.g. changing final\_maize.txt to hyph\_maize\_final.txt and the output files to chr”$i”\_maize\_hyph.txt)

10.	Sort all of these new files to be either ascending for ? files or descending for – files, and create files with new extensions:  
for i in *ques.txt; do sort –k3,1n $i > $i.increase;done  
for i in *hyph.txt; do sort –k3,1nr $i > $i.decrease;done  

11.	Make directories: intermediate\_hyph\_files, intermediate\_ques\_files, individual\_hyph\_files, and individual\_ques\_files.

12.	Moved final files (ending in either .increase or .decrease) to Individual\_hyph\_files and Individual\_ques\_files folders to clean things up. These files are the 20 for maize and 20 for teosinte that are required in the prompt:  
mv *.increase individual\_ques\_files/
mv *.decrease individual\_hyph\_files/

13.	Moved intermediate files (ending in .ques.txt or .hyph.txt) to the intermediate\_hyph\_files and intermediate\_ques\_files folders as well:   
mv *ques.txt intermediate\_ques\_files/  
mv *hyph.txt intermediate\_hyph\_files/  

14.	In order to generate the two individual files (unknown and multiple positions) for each corn variety, I went back to the files before subsitituting – for ? (files are named final\_maize.txt and final\_teosinte.txt). Pulled out just the file entries with “unknown” or “multiple” in their chromosome column using:  
awk -F'\t' '{if($2 ~ "unknown") print $0}' final\_maize.txt > unknown\_maize.txt  
awk -F'\t' '{if($2 ~ "multiple") print $0}' final\_maize.txt > multiple\_maize.txt  
The same was done for the teosinte files, and to confirm, I checked the unique values in the chromosome column using:   
cut -f2 final\_maize.txt | sort | uniq –c  
and comparing it to a word count of the lines in the new files.
The four files generated in this step are in the main folder of assignment1. 

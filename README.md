# Unix_assignment

## Data Inspection

### Attributes of fang_et_al_genotypes.txt and snp_position.txt

</> Bash

`ls -lh fang_et_al_genotypes.txt snp_position.txt`

`du -h fang_et_al_genotypes.txt snp_position.txt`

`head snp_position.txt`

`head fang_et_al_genotypes.txt`

`wc -l fang_et_al_genotypes.txt`

`wc -l snp_position.txt`

`awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt`

`awk -F "\t" '{print NF; exit}' snp_position.txt`

**By inspecting the files, it is known that**

1.fang_et_al_genotypes.txt:**File Size: 11 MB; Number of Lines: 2783; Number of Columns: 986 

2.snp_position.txt:**File Size: 81 KB; Number of Lines: 984; Number of Columns: 15 

## Data Processing

**Step 01** *Separate maize and teosinte samples from the fang_et_al_genotypes.txt 
</> Bash

`awk 'NR==1 || $3 ~ /ZMMIL|ZMMLR|ZMMMR/' fang_et_al_genotypes.txt > maize_genotypes.txt`

`awk 'NR==1 || $3 ~ /ZMPBA|ZMPIL|ZMPJA/' fang_et_al_genotypes.txt > teosinte_genotypes.txt`

The command aids to get only the required groups for the maize and teosinte genotypes

**Step 02** *Trnaspose the genotype files*

</> Bash

`awk -f transpose.awk maize_genotypes.txt > transposed_maize_genotypes.txt`

`awk -f transpose.awk teosinte_genotypes.txt > transposed_teosinte_genotypes.txt`

In the transposed file, the SNPs can be found in the rows so that merging with the SNPs position file will be easier.

**Step 03** *Sort the SNPs position and transposed files*

</> Bash

`tail -n+2 snp_position.txt | sort -f -k1 -V > snp_position_sorted.txt`

`tail -n+4 transposed_maize_genotypes.txt | sort -f -k1 -V > transposed_maize_genotypes_sorted.txt`

`tail -n+4 transposed_teosinte_genotypes.txt | sort -f -k1 -V > transposed_teosinte_genotypes_sorted.txt`

The command helps to remove the headers and all three files are sorted by the SNPs ID as a preparation for the joining step.


**Step 04** *Create a sorted file for the SNPs position*

</>Bash 

`cut -f1,3,4 snp_position_sorted.txt > snp_position_sorted_subset.txt`

In the sorted file, there will be SNP ID, chromosome number, and chromosome position. 

**Step 05** *Merge the maize and teosinte genotype data with the SNPs position data*

</> Bash

`join -1 1 -2 1 -t $'\t' snp_position_sorted_subset.txt transposed_maize_genotypes_sorted.txt > merged_maize_genotypes.txt`

`join -1 1 -2 1 -t $'\t' snp_position_sorted_subset.txt transposed_teosinte_genotypes_sorted.txt > merged_teosinte_genotypes.txt`

The data is merged based on the SNPs ID. 

**Step 06** *Sort maize data with increasing or decreasing chromosome  position values for the SNPs*

</> Bash

`for i in {1..10}; do awk -v chromosome="$i" '$2 == chromosome' merged_maize_genotypes.txt | sort -k3,3n > maize_chromosome"$i"_increasing.txt done`

`for i in {1..10}; do awk -v chromosome="$i" '$2 == chromosome' merged_maize_genotypes.txt | sort -k3,3nr > maize_chromosome"$i"_decreasing.txt done`

For the maize genotype, total 20 files are created based on the increasing and decreasing chormosomal position of the SNPs

**Step 07** *Separate the unknown and multiple data for the maize*

</> Bash

`awk '$2 == "unknown"' merged_maize_genotypes.txt > maize_unknown_positions.txt`

`awk '$2 == "multiple"' merged_maize_genotypes.txt > maize_multiple_positions.txt`

Two separate files are created foe the maize's unknown and multiple data.

**Step 08** *Sort teosinte data with increasing or decreasing chromosome position values for the SNPs*

</> Bash

`for i in {1..10}; do awk -v chromosome="$i" '$2 == chromosome' merged_teosinte_genotypes.txt | sort -k3,3n > teosinte_chromosome"$i"_increasing.txt done`

`for i in {1..10}; do awk -v chromosome="$i" '$2 == chromosome' merged_teosinte_genotypes.txt | sort -k3,3nr > teosinte_chromosome"$i"_decreasing.txt done`

For the teosinte genotype, total 20 files are created based on the increasing and decreasing chormosomal position of the SNPs

**Step 09** *Separate the unknown and multiple data for the teosinte*

</> Bash

`awk '$2 == "unknown"' merged_teosinte_genotypes.txt > teosinte_unknown_positions.txt`

`awk '$2 == "multiple"' merged_teosinte_genotypes.txt > teosinte_multiple_positions.txt`

Two separate files are created for the teosinte's unknown and multiple data.

**Step 10** *Move the processed data to the new folder*

</> Bash 

`mkdir maize_files teosinte_files`

`mv maize_* maize_files/`

`mv teosinte_* teosinte_files/`

`mkdir Unix_assignment_Shamima`

`mv maize_files teosinte_files Unix_assignment_Shamima/`

It will create the final folder ready to be uploaded to github.



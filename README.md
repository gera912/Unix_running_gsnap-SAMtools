# RNA-seq Data Exploration
The objective of this assignment is to practice your .fastq parsing skills and your ability to answer quality-related questions about raw sequencing data. We will work with a set of paired-end mRNA-Seq reads from the Gulf pipefish, which includes several multiplexed libraries. You will apply UNIX tools and short-read alignment to gather some information about the construction of the molecular libraries, and you will probe for adapter sequence, rRNA contamination, and homopolymers.

We will be working on Talapas for the next few assignments due to large file sizes and software requirements. Make sure to work in interactive mode for very small tasks, and submit sbatch scripts for everything else.

The RNA-Seq data consist of two files, one each for the forward and reverse reads, respectively, and can be found on Talapas here:
```
/projects/bgmp/shared/Bi623/assign4/PE_RNAseq_R1.fastq.gz
/projects/bgmp/shared/Bi623/assign4/PE_RNAseq_R2.fastq.gz
```
1.	Copy or link these files to a working directory on Talapas.
2.	Using UNIX commands, determine and report the total number of read pairs from the files, and the number of reads that failed the Illumina software’s “Chastity” filter.
3.	Again, using UNIX commands, generate a file containing all of the barcodes represented in the R1 file, and their abundances, in order from most to least abundant. You donʼt have to include the entire list in the document you turn in (the top 20 entries are fine), but make sure you report how many barcodes there are.
	
- a.	What type of barcodes were used, and what length are they?
	
- b.	Assuming these multiplexed libraries were originally combined in nearly identical proportions, how many actual libraries are represented in the pool? Remember, barcodes are subject to sequencing error.

- c.	Did the person that prepped and pooled these libraries get even sequencing coverage across them?
# Adaptor Contamination
4.	Since this sequencing library was constructed from fragmented RNA molecules, some of these molecules may even be shorter than the length of 1 mate from a paired-end read. When this occurs, the adaptor sequence adjacent to the original RNA sequence is sequenced along with the RNA itself. If this happens, here are the adaptor sequences expected to occur at the end of R1 and R2 reads, respectively:
-	R1 adaptor sequence: AGATCGGAAGAGCACACGTCTGAACTCCAGTCAC
-	R2 adaptor sequence: AGATCGGAAGAGCGTCGTGTAGGGAAAGAGTGT
5.	Using grep, determine if there is R1 adaptor sequence contamination, and R2 adaptor sequence contamination.

- a.	Try executing grep with different subsets of the adaptor sequence (e.g. the 5’ and 3’ ends of each). Do you see variation in the number of matches? If so, describe what you see and why it makes sense.
- b.	What could happen during the assembly process if the adaptor sequence is present in the raw data?
# rRNA Contamination
6.	A common question with mRNA-Seq data is to what extent rRNA “carry-over” occurred during library prep. To answer this question, do the following:
•	
- a.	Align the pipefish RNA-Seq reads to the ribosomal RNA sequences found in the file Gacu_rRNA.fasta using gsnap. If you haven’t used gsnap, first you’ll have to build a database from Gacu_rRNA.fasta using gmap_build, then you’ll have to align the reads (the R1 file is sufficient) to that database using gsnap. Whenever you are using software for the first time, make use of the manual and help file(s)! Here is a link to the gmap/gsnap manual: http://research-pub.gene.com/gmap/src/README
There are several Talapas modules for gsnap, but you can load the module as follows:
```
ml easybuild intel/2017a GMAP-GSNAP/2017-06-20
```

- b.	The rRNA sequences can be found on Talapas here:/projects/bgmp/shared/Bi623/assign4/Gacu_rRNA.fasta
	
- c.	k = 15 is fine when running gmap_build. When you run gsnap, use a fairly relaxed sequence identity threshold for mapping (allow 20 mismatches, so -m 20).
	
- d.	Here is an example for how to run gsnap:
```
gsnap -D path_to_db/rRNA -d rRNA --gunzip -m 20 -t 28 \
-O --split-output path_to_db/rRNA -A sam \
/projects/bgmp/shared/Bi623/assign4/PE_RNAseq_R1.fastq.gz
```
Note that here we’re splitting the output by alignment type. Look into what this means.

- e.	Using the SAM output files, find out what proportion of the total reads are likely from rRNA. Consider a read of rRNA origin if it aligns to the rRNA reference in any capacity (e.g. multiply, uniquely, translocally, etc.).	
- f.	Include all the steps above in a sbatch script.
# Examine homopolymers
7.	Find out approximately what proportion of the R1 reads contains a piece of a poly-A tail. Use regular expressions (e.g. with grep) to accomplish this. Assume that anything composed of at least 15 consecutive “A”s is likely a poly-A tail.
8.	Also run your script to count occurrences of 15 or more consecutive “C”s, “G”s, and “T”s and print their frequencies. Explain why the observed frequencies do or do not make sense, given what you know about this type of RNA-seq data.


Prior to uploading to Trillium, human reads need to be processed out of our mtx data. Previously, I have run bowtie2 which has yielded low mapping against the reference human genome and transcriptome. Running Kraken2 as a sanity check however did not validate these results; the percentage of reads identified as human by K2 is several orders of magnitude higher compared to BT2. However, selecting the first 100 reads that were identified as human by K2 and blasting them results in no human hits (see PANAMA_K2_BLAST_results.md). 

Many of the identified reads in the initial 100 have long G blocks at the 5' end of the sequence as demonstrated below
```
>LH00403:165:23GW2YLT4:1:1101:5255:1028 1:N:0:CNCACGACTG+CNGTTGGTCC
TCTGTCTCTTATACACATCTCCGAGCCCACGAGACCGCACGACTGATCTGGGGGGGCGTCCTTTTCTTTGGAAAAGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGG
```
In the initial 100, the vast majority of these reads are identified by BLASTn as Gallid alphaherpesvirus.

```
Species / max score / total score / query cover / E value / % ID
Gallid alphaherpesvirus 1	191	191	99%	3e-44	90.07%
```
Removing the Poly G block from this specific read however results in hits against *P. aeruginosa*, an Escherichia phage and *R. solanacearum* (a plant pathogen).
```
Pseudomonas aeruginosa	76.8	76.8	75%	3e-10	91.23%	
Escherichia phage vB_EcoM_G4507	73.1	73.1	65%	4e-09	93.88%
Ralstonia solanacearum	73.1	12449	65%	4e-09	93.88%
```
*R. solanacearum* is also pulled up as a hit for some other reads in this set as well. 

Running a couple of the G block bearing reads with the blocks removed in K2 to see if anything changes:
```
kraken2 --db "/home/fry/Bioinformatics_software/Kraken2/K2_DB" \
--threads 3 \
--output K2_results.txt \
--report K2_report.txt \
K2_vs_BLAST_test
Loading database information... done.
5 sequences (0.00 Mbp) processed in 0.001s (276.2 Kseq/m, 33.31 Mbp/m).
  3 sequences classified (60.00%)
  2 sequences unclassified (40.00%)
```
```
C       LH00403:165:23GW2YLT4:1:1101:5255:1028  9606    151     0:4 1:1 0:64 9606:5 0:43
C       LH00403:165:23GW2YLT4:1:1101:33837:1070 9606    151     0:4 1:1 0:64 9606:5 0:43
C       LH00403:165:23GW2YLT4:1:1101:32527:1126 131567  151     0:2 9606:5 0:60 2:5 0:6 1:1 0:38
U       tester:1        0       75      0:4 1:1 0:36
U       tester:2        0       75      0:4 1:1 0:36
```
When the G blocks are removed (at least for these 2 reads), K2 is unable to classify the reads... interesting. The read classified as taxid 131567 is one which does not bear a G block and was identified as human by K2 but *R. solanacearum* by BLAST, and was used as a control.


According to John (and as is evidenced by the change in results upon removal of the G block), we should be filtering out these repeat regions from our data. To get an idea of the quality of the reads that have been provided, I'm going to run FastP on my initial test sample (PANAMA-10_1_S7_L001). 

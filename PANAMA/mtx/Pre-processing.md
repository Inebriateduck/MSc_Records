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

According to John (and as is evidenced by the change in results upon removal of the G block), we should be filtering out these repeat regions from our data. To get an idea of the quality of the reads that have been provided, I'm going to run FastP on my initial test sample (PANAMA-10_1_S7_L001). 


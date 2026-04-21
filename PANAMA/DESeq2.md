```
ls()
] "metadata_df" "OTU"         "otu_df"      "otu_mat"     "physeq"      "random_tree" "RCADS_ps"    "SAMP"       
 [9] "TAX"         "tax_df"      "tax_mat"

RCADS_ps
phyloseq-class experiment-level object
otu_table()   OTU Table:         [ 330 taxa and 38 samples ]
sample_data() Sample Data:       [ 38 samples by 1 sample variables ]
tax_table()   Taxonomy Table:    [ 330 taxa by 7 taxonomic ranks ]

library("phyloseq")
library("DESeq2")

otu_table(RCADS_ps) <- round(otu_table(RCADS_ps))
sample_data(RCADS_ps)$High_RCADS <- factor(sample_data(RCADS_ps)$High_RCADS, levels = c("n", "y"))
dds_rcads <- phyloseq_to_deseq2(RCADS_ps, ~ High_RCADS)

dds_rcads <- estimateSizeFactors(dds_rcads, type = "poscounts")
dds_rcads <- DESeq(dds_rcads, test="Wald", fitType="parametric")

res <- results(dds_rcads, cooksCutoff = FALSE)
res <- res[order(res$padj, na.last=NA), ]
res_df <- as.data.frame(res)
tax_df <- as.data.frame(as(tax_table(RCADS_ps), "matrix"))
final_results <- merge(res_df, tax_df, by = "row.names")
colnames(final_results)[1] <- "Bin_ID"
write.table(final_results, "/scratch/fry/PANAMA/RCADS_DS2_Results.tsv", sep="\t", row.names=F, quote=F)
sig_hits <- subset(final_results, padj < 0.05)
sig_hits <- sig_hits[order(sig_hits$log2FoldChange, decreasing = TRUE), ]
sig_hits$Species_Clean <- sub("s__", "", sig_hits$Species)
head(sig_hits[order(sig_hits$padj), c("Bin_ID", "log2FoldChange", "Species_Clean")], 10)

    Bin_ID log2FoldChange                Species_Clean
59     236     -23.852126      Ruminococcus_C callidus
105     80     -23.856706   Streptococcus thermophilus
113     91     -23.999825      Bifidobacterium bifidum
85      50     -24.175377   Faecalibacterium duncaniae
21     148     -23.668812 Bifidobacterium adolescentis
104      8      23.234874 Bacteroides thetaiotaomicron
68     288      22.923869         Bacteroides fragilis
115     93      -8.854091          Agathobacter faecis
```

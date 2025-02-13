# OUTPUT FILES
The final two fasta files contain the same sequence of the mutated sequences in amino acid (pep) and nucleotide (CDS) format. Mutated sequence: [8 last codons of CDS] + [mutated STOP codon] + [3'UTR to downstream STOP codon]

# LOG SUMMARY
20 CDS' don't start with 'ATG'.
2554 CDS' don't end with one of the three STOP codons (TAG, TAA, TGA).
We loaded 206 723 cDNA.
921 CDS' were not matched to an ENST-ID in the cDNA file (5.04%).
13 mitochondrial genes were dropped.
This left us with 17356 genes with matched CDS-cDNA to predict peptide extensions.
Out of these 17356, 1282 transcripts had no downstream STOP in the 3'UTR and were discarded. It can be expected that these mutations would not result in translated peptides due to non-mediated decay.
The final fasta file of potential extensions contains 16074 genes for which we predicted the peptide extensions. A stop-loss mutation results in a codon that encodes one of six or seven different amino acids, depending on the specific stop codon affected. Therefore, we ended up with 100035 potential, unique peptide extensions.

# Predicting peptide extensions from stop-loss mutations in canonical proteins
Jupyter notebook computing potential peptide extensions from stop-loss mutations in human canonical sequences.

Author: (Lilian Marie Boll)[https://justalilibit.github.io/] (lilianboll@mailbox.org)

## Create vcf file with all possible peptide elongations resulting from a SNV in a STOP codon
1. Get the nucleotide sequences of all canonical peptides
    - Downloaded Coding Sequences (cds) from ensembl (https://ftp.ensembl.org/pub/release-112/fasta/homo_sapiens/).

    Function *parse_cds_to_dict()*
    - 1.0 Check if gene_symbol already processed previously. If yes, skip to next.
    - 1.1 Find first ATG in cds, if not move to next and print to std.out
    - 1.2 Check for downstream STOP codon, if not print std.out
    - 1.3 If ATG and STOP found, store gene_symbol, ENSG, ENST and description in df
    - 1.4 Store gene_symbol and CDS in seq object
    - 1.5 Mitochondrial genes will be dropped as they follow diffferent rules of translationa nd transcription and often have no UTRs.

    After this step, we obtain 19 360 CDS sequences of unique genes that have a "valid sequence"

2. Get cDNA with ENST-ID of CDS
    - 2.1 Create a cdna_dict from cDNA ensmbl file with ENST_ID:cDNA_seq (function *build_cdna_dict()*)
    Function *extract_enstID()* to get transcript ID of CDS entry
    Function *create_enst_dict()* creates dictionary with gene:ENST_ID
    - 2.2 Function *add_cdna_to_cds()* to match CDS with cDNA using the transcript ID (ENST)
    
    For 892 of the 19 360 CDS, we found no matching ENST-ID in the cDNA.

3. Mutating the STOP codon of the 18 467 CDS
    - 3.1 Function *trim_cdna_to_overlap()* will keep only the 3'UTR of the cDNA (seq after end of CDS).
    - 3.2 Function *find_ds_STOP_in3UTR()* checks for downstream STOP codons in the 3'UTR. If none found, the entry will be discarded.
    Out of 18468, we removed 300 genes due to missing dsSTOP (1.62%).
    - 3.3 Function *generate_mutated_sequences_with_utr()* introduces all possible non-silent mutations in the original STOP codon of the CDS. 
    The possibile mutated codons are stored in nonsilentSNV_dict.
    - 3.4 Function *translate_mutated_cds_to_pep()* uses Bio.Seq object to translate the elongtaed CDS to peptide

4. Write fasta files
    - 4.1 Function *update_header()* will create a unique ID for each mutated seq in the format >[gene_symbol]_n 
    - 4.2 Function *write_fasta_files()* creates 2 output files:
        - peptide seq in amino acids
        - mutated and elongated CDS in nucleotides

## Output:
The final two fasta files contain the exact same sequneces of the mutated sequences
in amino acid (pep) and nucleotide (CDS) format.
Mutated sequence: [8 last codons of CDS] + [mutated STOP codon] + [3'UTR to downstream STOP codon]


## Versions
- Genome GRCh38, Ensembl release 112 | files from [ensembl.org](https://ftp.ensembl.org/pub/release-112/fasta/homo_sapiens/)
- Python 3.8.10 (default, Jan 17 2025, 14:40:23) 
- [GCC 9.4.0]

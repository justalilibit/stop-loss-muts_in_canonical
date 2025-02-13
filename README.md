# Predicting peptide extensions from stop-loss mutations in canonical proteins

Author: [Lilian Marie Boll](https://justalilibit.github.io/)


## INPUT FILES
We used the genome GRCh38, Ensembl release 112 | files from [ensembl.org](https://ftp.ensembl.org/pub/release-112/fasta/homo_sapiens/)

__Coding Sequences (CDS)__:
18290 CDS' in fasta format ([Download link for CDS fasta file](https://ftp.ensembl.org/pub/release-112/fasta/homo_sapiens/cds/Homo_sapiens.GRCh38.cds.all.fa.gz))

__Coding DNA (cDNA)__:
206723 cDNA sequences in fasta format ([Download link for cDNA fasta file](https://ftp.ensembl.org/pub/release-112/fasta/homo_sapiens/cdna/Homo_sapiens.GRCh38.cdna.all.fa.gz))

_Both fasta files were last accessed on Feb 13th 2025._


## JUPYTER NOTEBOOK
1. Get the nucleotide sequences of all canonical peptides
    - Downloaded Coding Sequences (cds) from ensembl (https://ftp.ensembl.org/pub/release-112/fasta/homo_sapiens/).

    Function *parse_cds_to_dict()*
    - 1.0 Check if gene_symbol already processed previously. If yes, skip to next.
    - 1.1 Find the first ATG in cds, if not move to next and print to std.out
    - 1.2 Check for downstream STOP codon, if not print std.out
    - 1.3 If ATG and STOP found, store gene_symbol, ENSG, ENST, and description in df
    - 1.4 Store gene_symbol and CDS in seq object
    - 1.5 Mitochondrial genes will be dropped as they follow different rules of translation and transcription and often have no UTRs.

2. Get coding DNA (cDNA) with ENST-ID of CDS
    - 2.1 Create a cdna_dict from cDNA ensmbl file with ENST_ID:cDNA_seq (function *build_cdna_dict()*)
    Function *extract_enstID()* to get transcript ID of CDS entry
    Function *create_enst_dict()* creates dictionary with gene:ENST_ID
    - 2.2 Function *add_cdna_to_cds()* to match CDS with cDNA using the transcript ID (ENST)

3. Mutating the STOP codon of the  CDS
    - 3.1 Function *trim_cdna_to_overlap()* will keep only the 3'UTR of the cDNA (seq after end of CDS).
    - 3.2 Function *find_ds_STOP_in3UTR()* checks for downstream STOP codons in the 3'UTR. If none is found, the entry will be discarded.
    - 3.3 Function *generate_mutated_sequences_with_utr()* introduces all possible non-silent mutations in the original STOP codon of the CDS. 
    The possible mutated codons are stored in nonsilentSNV_dict.
    - 3.4 Function *translate_mutated_cds_to_pep()* uses Bio.Seq object to translate the extended CDS to peptide
    - 
![Schema of possible stop-loss mutations](https://github.com/justalilibit/stop-loss-muts_in_canonical/blob/main/stop-loss_mutation_aa_change.png)

4. Write fasta files
    - 4.1 Function *update_header()* will create a unique ID for each mutated seq in the format >[gene_symbol]_n 
    - 4.2 Function *write_fasta_files()* creates 2 output files:
        - peptide seq in amino acids
        - mutated and extended CDS in nucleotides

## OUTPUT:
The final two fasta files contain the same sequence of the mutated sequences
in amino acid (pep) and nucleotide (CDS) format.
Mutated sequence: [8 last codons of CDS] + [mutated STOP codon] + [3'UTR to downstream STOP codon]

- 20 CDS' don't start with 'ATG'.
- 2554 CDS' don't end with one of the three STOP codons (TAG, TAA, TGA).
- We loaded 206 723 cDNA.
- 921 CDS' were not matched to an ENST-ID in the cDNA file (5.04%).
- 13 mitochondrial genes were dropped.
- This left us with 17356 genes with matched CDS-cDNA to predict peptide extensions.
- Out of these 17356, 1282 transcripts had no downstream STOP in the 3'UTR and were discarded. It can be expected that these mutations would not result in translated peptides due to non-mediated decay.
- The final fasta file of potential extensions contains 16074 genes for which we predicted the peptide extensions. A stop-loss mutation results in a codon that encodes one of six or seven different amino acids, depending on the specific stop codon affected. Therefore, we ended up with 100035 potential, unique peptide extensions. 


## VERSIONS
- Genome GRCh38, Ensembl release 112 | files from [ensembl.org](https://ftp.ensembl.org/pub/release-112/fasta/homo_sapiens/)
- Python 3.8.10 (default, Jan 17 2025, 14:40:23) 
- [GCC 9.4.0]

# TranspoMerge
## Authors
Abdelrahman Eldreeny 

Omar Loay 

Sama El-Taher 

## Tool Description
TranspoMerge is used to detect transposable elements in bacterial genomes by identifying the direct repeats characteristic of these elements. The direct repeats are identified by aligning the short paired-end reads of the bacterial sample with a reference genome and collecting the soft-clipped reads as candidate termini. These termini are paired together and filtered based on orientation, position, length, and count. The termini are then used to retrieve the entire mobile genetic element sequence from the sample’s assembled genome, which is subsequently annotated.

The annotation method is a Python script that uses BLAST (Basic Local Alignment Search Tool) and Entrez services provided by the National Center for Biotechnology Information (NCBI). This script annotates sequences in a FASTA file and retrieves detailed gene information for each sequence. The annotation results are saved to a CSV file for further analysis.

## How to Install It
TranspoMerge can be installed on macOS, Windows, and Linux. The required dependencies are pandas, Biopython, regex, random, numpy, and pysam. Ensure all reference and alignment files (.bam) are indexed. Follow these steps for proper installation:

1. Navigate to [GitHub](https://github.com/)
2. Find the repository named “TranspoMerge”
3. Click on the code button and clone the repository to download the TranspoMerge files
4. Use the `git clone` command followed by the repository URL

**Note**: The tool files are not yet available on GitHub. You can find the files in the installed SCR directory.

## How to Use It
To detect possible candidates as insertion termini, use the aligned file generated by BWA-MEM as input for the script TermFind. 

1. Call `TermFind.get_contig_info(bam_path)` to get the necessary information from the BAM file, which is saved in a `TermFind.contig_dict` dictionary. The BAM file is parsed using the Alignment module of pysam.
2. Call `TermFind.parse_softclips()` to add the soft-clipped reads and all relevant information to dictionaries.
3. Filter the soft-clipped reads based on minimum length and count using `TermFind.filter_softclips_minlength()` and `TermFind.filter_softclips_mincount()`, respectively.
4. Extract and store the consensus sequences from each read in different dictionaries (whether 5' or 3') by calling `TermFind.make_consensus_sequences()`.
5. Further filter these sequences based on minimum length and count using `TermFind.filter_consensus_sequences_minlength()` and `TermFind.filter_consensus_sequences_mincount()`.
6. Produce a TSV file by calling `TermFind.make_dataframe()`.
7. Pair the termini using `PairFlank.pair(tsv_file, reference_genome)`, then retrieve the complete insertion sequence using `FullSeq.fullseq(final_pairs, sample_seq)`.
8. Finally, annotate and store the insertion sequences in a CSV file using `Annotation(email, insrt_seqs)`, `annotation.perform_blast_search()`, and `save_to_csv(gene_info)`.

## How to Run a Demo
The demo will analyze one sample of Staphylococcus aureus using the flow described in the "How to Use It" section. Use the following files as input:

1. The `ref_sequence.fasta` file as a reference genome.
   - Retrieve the S. aureus reference genome using the command:
     ```bash
     wget https://www.ncbi.nlm.nih.gov/nuccore/NC_017349
     ```
2. The sample paired-end read fastq file.
   - Retrieve the sample’s paired-end read fastq file using the command:
     ```bash
     wget https://www.ebi.ac.uk/ena/browser/view/SAMEA3573598?show=reads
     ```
   - Perform alignment and index the BAM file using the commands:
     ```bash
     bwa mem ref.fa S_0181_1.fastq S_0181_.fastq > aln-pe.sam
     bwa index ref.fa
     ```
3. Assemble the sample’s paired-end reads using the command:
   ```bash
   spades.py [options] -o <output_dir>

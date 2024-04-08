---
title: 4. Sequence manipulation
---
>[!summary] In this chapter
> - Manipulation of sequence files (`FASTA`, `FASTQ`) using seqkit.

## seqkit
[Repository](https://github.com/shenwei356/seqkit) || [Documentation](https://bioinf.shenwei.me/seqkit/usage/)

SeqKit is a versatile and high-performance command-line toolkit specifically designed for the manipulation and analysis of FASTA and FASTQ files. 

This toolkit streamlines a wide array of bioinformatics tasks, from basic sequence manipulation, such as conversion between FASTQ and FASTA formats, sequence sorting, and deduplication, to more complex analyses like sequence statistics generation, sample filtering by various criteria (length, quality, ID), and even sequence transformation operations (e.g., complementing and reverse-complementing sequences).

Installation is straightforward with conda:
```bash
conda install -c bioconda seqkit
```

SeqKit offers a comprehensive suite of functionalities, including:
- **FASTQ to FASTA Conversion:** Convert FASTQ files to FASTA format.
	```bash
	seqkit fq2fa sequences.fq -o sequences.fa
	```

- **FASTA Formatting:** Reformat multi-line FASTA files to 4-line FASTA format.
	```bash
	seqkit seq reads_1.fa -w 0
	```

- **Reverse Complementary:** Convert sequences to their reverse complement.
	```bash
	 seqkit seq hairpin.fa.gz --reverse --complement
	```

- **Gap Removal:** Eliminate gaps within sequences.
	```bash
	seqkit seq --remove-gaps
	```

- **Sequence Length Filtering:** Filter sequences based on their length.
	```bash
	seqkit seq sequences.fa --min-len 100 --max-len 1000
	seqkit seq sequences.fa --min-len 100 --max-len 1000
	```

- **Sequence Statistics:** Generate detailed statistics for sequences, such as count, min/max/average length, etc. Supports multiple files.
	```bash
	seqkit stats sequences.fa
	seqkit stats *.f{a,q}.gz
	```

- **Histograms of Sequence Attributes:** Create histograms for sequence lengths or average quality scores.
	```bash
	seqkit watch --fields ReadLen sequences.fq.gz -O histogram_len.png
	seqkit watch --fields MeanQual sequences.fq.gz -O histogram_quality.png
	```
	Available fields for histogram: ReadLen, MeanQual, GC, GCSkew.
	
- **Translation to Amino Acids:** Translate nucleotide sequences to amino acids.
	```bash
	seqkit translate c.fna
	```

- **Sequence Name Extraction:** Extract sequence names or identifiers.
	```bash
	seqkit seq hairpin.fa.gz --name
	seqkit seq hairpin.fa.gz --name --only-id
	```

- **RNA to DNA Conversion:** Transform RNA sequences to DNA.
	```bash
	seqkit seq sequences.fa --rna2dna
	```

- **Sequence Filtering by ID:** Filter sequences by specific IDs.
	```bash
	seqkit grep -p "d9321e7b-3969-4ecd-9bb5-e85d4c59d86f" sequences.fa
	seqkit grep -f ids_to_filter.txt sequences.fa
	```
	Use `-v` for reverse filtering, i.e., to exclude specified IDs.
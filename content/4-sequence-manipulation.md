---
title: 4. Sequence manipulation
---
>[!summary] In this chapter
> - Manipulation of sequence files (`fasta`, `fastq`) using seqkit.

## seqkit
[Repository](https://github.com/shenwei356/seqkit) || [Documentation](https://bioinf.shenwei.me/seqkit/usage/)

Seqkit is a efficient tool for manipulating and processing FASTA and FASTQ files.

It can be installed via conda.
```bash
conda install -c bioconda seqkit
```

- Convert FASTQ file to FASTA format.
	```bash
	seqkit fq2fa sequences.fq -o sequences.fa
	```

- Convert a multi-line FASTA file to a 4-line FASTA format.
```bash
seqkit seq reads_1.fa -w 0
```

- Convert sequences to their reverse complementary
 ```bash
 seqkit seq hairpin.fa.gz --reverse --complement
```

- Remove gaps within sequences.
```bash
seqkit seq --remove-gaps
```

- Filter sequences by length.
```bash
seqkit seq sequences.fa --min-len 100 --max-len 1000
seqkit seq sequences.fa --min-len 100 --max-len 1000
```

- Get basic statistics of the sequences (number of sequences, minimum and maximum length, average length, etc.). Statistics can be obtained for multiple files simultaneously.
```bash
seqkit stats sequences.fa
seqkit stats *.f{a,q}.gz
```

- Obtain a histogram of sequence sizes or average quality.
```bash
seqkit watch --fields ReadLen sequences.fq.gz -O histogram_len.png
seqkit watch --fields MeanQual sequences.fq.gz -O histogram_quality.png
```
	Available fields for histogram: ReadLen, MeanQual, GC, GCSkew.
	
- - Translate nucleotide sequences to aminoacids.
```bash
seqkit translate c.fna
```

- Print sequence names, or their identifiers only.
```bash
seqkit seq hairpin.fa.gz --name
seqkit seq hairpin.fa.gz --name --only-id
```

- Convert RNA sequences to DNA.
```bash
seqkit seq sequences.fa --rna2dna
```

- Filter sequences by ID.
```
 seqkit grep -p "d9321e7b-3969-4ecd-9bb5-e85d4c59d86f" sequences.fa
 seqkit grep -f ids_to_filter.txt sequences.fa
```
The `-v` option can be used for reverse search, i.e., selecting all reads that are not in the specified list.
---
title: 5. Genome assembly
---
## What is genome assembly?

Genome assembly aims to piece together DNA fragments, using overlaps between reads or other techniques, to ideally obtain a single contig with the reconstructed genome. One of the most common approaches to assembly involves searching for overlaps among reads to assemble them into longer contigs.

There are two main types of assembly: "*denovo* assembly" and "comparative assembly". Denovo assembly relies solely on the information generated during sequencing to reconstruct the genome, while comparative assembly uses a reference genome as a basis for the assembly.

Second-generation sequencing technologies produce reads ranging from 20bp to 500bp, making the assembly of repetitive regions or longer regions challenging. Thus, third-generation sequencing technologies have been instrumental in reducing costs and complexities when assembling genomes or regions with the aforementioned characteristics, thanks to their ability to generate reads of several kilobases in length.

![[long_vs_short_read_assembly.png]]

The NCBI hosts a platform that allows visualization of all assemblies uploaded to its database, including their size, taxonomic description, and the number of assemblies per organism. ([Website](https://www.ncbi.nlm.nih.gov/genome/browse/#!/overview/))

Key terms in this field include:
- `Contig (Contiguous sequence)`: A DNA sequence that results from the assembly of two or more sequences overlapping at their ends, joined into a single, non-redundant sequence without gaps.
- `Scaffold`: Sequences formed from joining contigs. They may contain gaps and are generally used for chromosome assembly.
- `Draft genome`: An unfinished genome assembly that typically covers between 90% and 95% of the target genome.
- `Annotation`: The process of assigning a function to a known gene.

![[genome-assembly.png]]

This translation and improvement clarify the concepts and processes involved in genome assembly, highlighting the distinctions between assembly types and the advancements in sequencing technologies.

## Tools

Genome assembly commonly involves multiple steps and tools, assemblers, polishers, scaffolders, and quality control tools. There are several tools available for each step, and the choice of tools depends mostly on the data generated and the desired output.

### Assemblers

There are multiple assemblers available, such as `SPAdes`, `Canu`, `Flye`, among others. Oxford Nanopore recommends using `Flye` for its ability to assemble long reads and its high accuracy.

#### Flye

[Repository](https://github.com/fenderglass/Flye) || [Documentation](https://github.com/fenderglass/Flye/blob/flye/docs/USAGE.md)|| [Article](https://www.nature.com/articles/s41587-019-0072-8)

Flye is a state-of-the-art _de novo_ assembler designed for single-molecule sequencing reads, such as those from Oxford Nanopore Technologies and PacBio. Flye provides a comprehensive workflow that includes read correction prior to assembly, enhancing the accuracy of the assembly process. It is capable of assembling complete genomes and metagenomes.

Flye offers a range of parameters tailored to the sequence being assembled, which vary based on the error rate and sequencing technology:
- `--pacbio-raw`: For PacBio CLR reads with less than a 20% error rate.
- `--pacbio-corr`: For corrected PacBio reads with less than a 3% error rate.
- `--pacbio-hifi`: For PacBio HiFi reads, featuring less than a 1% error rate.
- `--nano-raw`: For Oxford Nanopore reads from Guppy versions below 5, with less than a 20% error rate.
- `--nano-corr`: For corrected Oxford Nanopore reads with less than a 3% error rate.
- `--nano-hq`: For high-quality Oxford Nanopore Q20 reads or those from Guppy versions above 5, with less than a 5% error rate.

Flye doesn't require the expected genome size to be specified for assembly, but this can be provided using the `--genome-size` parameter. For metagenome assembly, the `--meta` parameter indicates this intention. Additionally, if you have an assembly obtained with another tool, `--polish-target` allows for polishing of the assembly of interest.

A straightforward Flye execution example using default parameters is shown below:
```bash
flye --nano-hq sequences.fasta --out-dir out_nano --threads 4
```

Flye outputs three key files:
- `assembly.fasta`: The final assembly.
- `assembly_graph.{gfa|gv}`: The generated graph of repeats.
- `assembly_info.txt`: Information on the assembled contigs (size, coverage, circular genome indication, repetitive genome indication, etc.).

>[!tip] Reducing usage of computational resources
>Utilizing the `--asm-coverage` and `--min-overlap` parameters can help reduce memory usage, computational resources, and runtime.

### Polishers

#### Medaka

[Repository](https://github.com/nanoporetech/medaka)

Medaka is a tool developed by Oxford Nanopore Technologies, specifically designed for polishing assemblies or consensus sequences to improve their accuracy.

Installation is easy with conda:
```bash
conda install -c bioconda medaka
```

Medaka employs models to enhance consensus accuracy and variant calling, based on the basecaller used for sequencing. The available models can be listed using the command `medaka tools list_models`. The choice of model depends on the sequencing chemistry and pore used, and the type of basecalling. Model names follow this format:

```bash
{chemistry}_{pore}_{speed}_{caller variant}_{caller model version}

r1041_e82_400bps_sup_v4.3.0
# Data obtained from sequencing using the 10.4.1 chemistry with pore E8.1 at 400bps.
# Basecalling was performed with the sup model and model v4.3.0 of Dorado.
```

Keeping Medaka updated is essential; using outdated versions may result in the absence of the most suitable models, impacting the quality of consensus sequences or variant detection.

Here's a simple Medaka command to generate a polished consensus:
```bash
medaka_consensus -o assembly_medaka -t 4 -i sequences.fasta -d draft_assembly.fasta
```

If a model isn't specified, the default will be used (which can be checked with `medaka_consensus -h`). To specify a model, use the `-m` parameter. Below is an example including a specific model:
```bash
medaka_consensus -o assembly_medaka -t 4 -i basecalled_reads.fastq -d draft_assembly.fasta -m r1041_e82_400bps_sup_v4.3.0
```

This approach ensures that Medaka can tailor its processing to the specific characteristics of the input data, resulting in higher accuracy of the final assembled sequences.

### QC tools

#### Quast
[Repository](https://github.com/ablab/quast) || [Documentation](http://bioinf.spbau.ru/quast)

Quast is a comprehensive tool for evaluating genome or metagenome assemblies by computing a wide array of metrics. It facilitates the comparison of metrics across different assemblies and also allows for comparison against a reference sequence.

Installation is straightforward with conda, and you can easily check the version and available commands:

```bash
conda install -c bioconda quast
quast --help
quast --version
```

Quast provides detailed reports that include essential metrics such as contiguity (e.g., N50, L50), quality (e.g., number of mismatches, indels), and completeness (e.g., number of covered reference genes). These metrics are vital for assessing the accuracy and integrity of assembled sequences, making Quast an invaluable tool for researchers working on genome assembly projects. Its ability to generate visual reports further aids in the intuitive understanding and comparison of assembly quality.

#### CheckM2
[Repository](https://github.com/chklovski/CheckM2) || [Article](https://www.nature.com/articles/s41592-023-01940-w)

CheckM2 is a tool designed for assessing the quality of microbial genomes recovered from isolates, single cells, and metagenomes. It offers an extensive suite of analyses to evaluate the completeness and contamination levels of these genomes. This tool is particularly valuable for microbial ecology studies and metagenomic projects where accurate genome quality assessment is crucial.

CheckM2 uses a machine learning model trained on simulated genomes with known levels of completeness and contamination, benchmarked, and subsequently applied to MAGs from a range of different environments.

Installation can be performed via conda, and check the help and installed version:
```bash
conda install -c bioconda checkm2
checkm2 --help
checkm2 --version
```

CheckM2 operates with a required database for making its predictions. You can download this database directly through the tool:
```bash
# This will download the database at "checkm2_db/CheckM2_database/uniref100.KO.1.dmnd"
checkm2 database --download --path checkm2_db
```

Once the database is downloaded, you can perform predictions on one or multiple files, specifying the `-i` argument, either by specifying them as `genome1.fasta genome2.fasta ...` or by a wildcard, like `genomes/*.fasta`. In this case, the `database_path` argument is set to the location of the previous command (the `.dmnd` file).
```bash
checkm2 predict \
	--input genome1.fasta \
	--output-directory checkm2_results \
	--database_path checkm2_db/CheckM2_database/uniref100.KO.1.dmnd \
	--threads 4
```

CheckM2 provides the results in a CSV file, where the most important columns are **Completeness** and **Contamination**. Recommended values for high quality genomes (either isolated or metagenome-assembled) are completeness > 90% and contamination < 5%.
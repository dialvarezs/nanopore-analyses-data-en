---
title: 3. Quality control and filters
---
>[!summary] In this chapter
> - How to perform quality control using tools such as FastQC and NanoPlot and understand the results provided by these tools
> - How to summarize statistics for a set of samples
> - How to filter your data using tools like Fastp and Cutadapt

## Quality control

The quality check process is a critical step when analyzing raw data. Performing thorough quality checks to filter out low-quality reads and remove adapters is crucial before initiating any analysis.

### What is quality?

All `FASTQ` files contain quality data encoded in ASCII format.

![[fq.png]]

Every ASCII character has a Q-Score equivalence. These equivalences are displayed in the following table.

![[illumina_fastq_coding.png]]

You can calculate the probability of error in the nucleotide using the formula `10^(-Q/10)`.

| Phread Quality Score | Error probabilty | Accuracy |
| -------------------- | ---------------- | -------- |
| 10                   | 1 in 10          | 90%      |
| 20                   | 1 in 100         | 99%      |
| 30                   | 1 in 1000        | 99,9%    |
| 40                   | 1 in 10000       | 99,99%   |

### Quality control tools
#### FastQC

[Repository](https://github.com/s-andrews/FastQC) || [Oficial website](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/) || [Documentation](https://dnacore.missouri.edu/PDF/FastQC_Manual.pdf)

FastQC is a versatile tool that simplifies the process of obtaining quality metrics from fASTQ files. It supports inputs in `.fastq`, `.bam`, or `.sam` formats. After processing, FastQC produces an HTML report, wherein each quality metric is presented as a module and assigned a status—Approved, Warning, or Fail—based on the quality assessment. These status indicators are calibrated primarily for data from second-generation sequencing platforms, known for their high-quality outputs, specially directed towards Whole Genome Sequencing (WGS). As a result, when analyzing data from long-read technologies such as Nanopore sequencing, it is common to see modules marked as failed. Consequently, it is essential to have a good understanding of your data's nature and to interpret the FastQC metrics with the sequencing technology's characteristics in mind.

  
FastQC provides the following modules for analyzing sequencing data quality:
- **Basic Statistics:** Presents basic file information such as the number of reads, minimum and maximum read length, and GC content.
- **Per Base Sequence Quality:** Graphs quality scores across read positions (X-axis) with the following boxplot elements:
    - The median value is represented by the central red line.
    - The inter-quartile range (25-75%) is shown as a yellow box.
    - The 10% and 90% points are marked by the upper and lower whiskers.
    - The mean quality is depicted by a blue line.
    - Warnings are issued if the lower quartile for any base is below 10, or if the median for any base is below 25.
    - Failures occur if the lower quartile for any base falls below 5, or the median for any base is under 20.
- **Per Sequence Quality Scores:** Displays the distribution of reads across quality scores (X-axis: quality score, Y-axis: number of reads).
    - Warnings are raised if the most frequently observed mean quality is under 27 (equating to a 0.2% error rate).
    - Failures are noted if the most frequently observed mean quality is below 20 (equating to a 1% error rate).
- **Per Base Sequence Content:** Shows the proportion of each of the four standard DNA bases along the read. Each base (A, C, G, T) is represented by a different color, as indicated in the plot legend.
- **Per Sequence GC Content:** Examines the GC content across the entire length of each sequence (X-axis: GC content, Y-axis: number of sequences).
- **N Content by Base:** The percentage of base calls at each position identified as 'N'.
- **Sequence Length Distribution:** The distribution of fragment sizes within the dataset.
- **Duplicate Sequences:** Assesses the level of duplication for each sequence in the library, indicating relative numbers of sequences with various degrees of duplication. A low level of duplication may suggest high coverage, while high duplication levels often imply enrichment bias.
	*By default, analysis is limited to the first 100,000 sequences in each file, and reads over 75bp are truncated to 50bp for this analysis.*
- **Over-represented Sequences:** Lists sequences that comprise more than 0.1% of the total. FastQC searches a database for matches to common contaminants and reports the best match found.
	*By default, analysis is limited to the first 100,000 sequences in each file, and reads over 75bp are truncated to 50bp for this analysis.*
- **Adapter Content:** Indicates the cumulative percentage of the library showing each of the adapter sequences at every position. FastQC includes adapters from Illumina, Nextera, and Solid.

Is it possible to install FastQC via Conda:
```bash
conda install -c bioconda fastqc
```

To verify that the tool has been installed correctly, you can view the version of the tool and its help:
```bash
fastqc --version
fastqc --help
```

Running FastQC only requires the input file, but you can also specify different parameters such as the number of threads to use and the directory where to save the output.  The output directory must be created before running the command.
```bash
fastqc <file.fastq>
fastqc <file.fastq> --outdir <fastqc_report> --threads 4
```

Several parameters can be specified, such as the minimum length of the reads to use `--min_length`, specifying a fasta file with contaminants (`--contaminants`) or adapters (`--adapters`), etc. All the parameters are defined in the help section of the tool.

Executing FastQC without arguments and if it is running in system with graphical interface will open a desktop application. In this application you can load the FASTQ file and visualize the reports.

#### Nanoplot
[Repository](https://github.com/wdecoster/NanoPlot) 

Nanoplot is a quality check tool specifically designed for long read data. There is an online version where you can upload files, but is limitated only a maximum file size of 100MB ([Nanoplot](http://nanoplot.bioinf.be)).

Installation of NanoPlot via Conda is straightforward:
```bash
conda install -c bioconda nanoplot
```

To ensure successful installation, you can check the version of NanoPlot and access its help documentation:
```bash
NanoPlot --help
NanoPlot --version
```

Running NanoPlot typically requires just the input file. However, users can also specify the number of processing threads and the output directory. By default, if no output directory is specified, NanoPlot saves the output files in the current working directory.
```bash
NanoPlot --fastq bc02.fq --outdir bc02 
```

Additionally, users can define the minimum and maximum read lengths for analysis and specify a prefix for the output files. This last option is necessary when aggregating reports with tools like MultiQC.
```bash
NanoPlot --threads 4 --fastq bc02.fq --outdir bc02 --minlength 1000 --maxlength 2000 --prefix bc02
```

NanoPlot generates an HTML report featuring interactive plots and a summarized text report.

#### MultiQC
[Documentation](https://multiqc.info/docs) 

MultiQC aggregates results from various bioinformatics tools across multiple samples into a unified report, streamlining the analysis process. By scanning a specified directory for analysis logs, MultiQC assembles a comprehensive HTML report.

Supported by MultiQC are log files from tools such as `fastqc`, `fastp`, `cutadapt`, `NanoPlot`, `jellyfish`, among others. For a complete list of supported modules, refer to the official documentation ([MultiQC modules](https://multiqc.info/docs/#multiqc-modules)).

MultiQC can be installed via Conda, and you can verify the installation by checking the version or accessing help:
```bash
conda install -c bioconda multiqc

multiqc --help
multiqc --version
```

Examples of usage:
```bash
multiqc <path_to_fastqc_results>
multiqc <path_to_NanoStats_out.txt> --module nanostat
multiqc <path_to_fastqc_results> --filename multiqc_raw_data
```

## Quality filtering
#### Fastp
[Repository](https://github.com/OpenGene/fastp)

Fastp is a comprehensive tool for processing FASTQ format files, offering features like adapter trimming, quality filtering, trimming by quality or specific positions, and filtering reads by minimum and maximum length.

Installation of Fastp can be done via Conda, along with commands to check its version and help documentation:
```bash
conda install -c bioconda fastp

fastp --help
fastp --verson
```

Fastp can handle both single-end and pair-end sequencing data. Here are examples of running Fastp with default settings for these sequencing types:
```bash
fastp -i seq.fastq -o seq_filtered.fastq  # Single end data
fastp --in1 in.R1.fq.gz --in2 in.R2.fq.gz --out1 out.R1.fq.gz --out2 out.R2.fq.gz  # Pair end data
```
Using Fastp's default filters applies criteria like a quality of Q15, adapter detection for Illumina, and a minimum size of 15bp. It’s import to align these default settings with your data’s requirements.

Fastp's default filtering strategy evaluates if a read's bases meet a certain quality percentage, typically requiring 60% of bases to be at or above Q15. Alternatively, the `-e` option allows for filtering based on the average quality of a read.

Some parameters to consider when using Fastp:
- `--disable_quality_filtering (-Q)`: Turns off Fastp's default quality filter.
- `--qualified_quality_phred (-q)`: Sets the minimum quality for a base to be considered high-quality (default 15).
- `--average_qual (-e)`: Establishes the minimum average quality for a sequence.
- `--unqualified_percent_limit (-u)`: Sets the allowable percentage of bases failing the quality threshold (default 40%).
- `--disable_adapter_trimming (-A)`: Disables default adapter filtering.
- `--adapter_fasta`: Specifies a custom adapter file for removal if default filtering is disabled.
- `--length_required`: Sets a minimum length threshold for reads.
- `--length_limit`: Sets a maximum length threshold for reads.
- `--cut_front`: Remove bases at the start of the sequence (5') that have an average quality lower than specified with `cut_mean_quality`. A 4bp window is used to calculate average quality (modifiable via `cut_window_size`).
- `--cut_tail`: Remove bases at the end of the sequence (3') that have an average quality lower than specified with `cut_mean_quality`.
- `--cut_mean_quality`: Minimum quality used for trimming the ends of the sequence for average quality (when using parameters like `cut_front`, `cut_tail`) (default is Q20).
- `--trim_front`, `--trim_tail`: Enables fixed-length trimming from sequence ends.
- `--max_len`: Trims reads exceeding a specified length at 3'.
- `--disable_trim_poly_g`: Disables the removal of polyG tails.

Executing Fastp with custom parameters might look like this:
```bash
fastp -i <fastq_file> -o <output_file> --html fastp_report.html \
  --disable_trim_poly_g --disable_adapter_trimming \
  --thread <number_of_cores> -q <minimum_quality> \
  --length_required <minimum_size> \
  --length_limit <maximum_size> \
  --cut_front \
  --cut_tail \
  --cut_mean_quality <minimum_quality>

fastp -i input.fastq -o output.fastq --html fastp_report.html \
  --disable_trim_poly_g --disable_adapter_trimming \
  --thread 6 -q 15 \
  --length_required 1000 \
  --length_limit 2000 \
  --cut_front \
  --cut_tail \
  --cut_mean_quality 12
```

It's worth noting that `fastp` offers many more options for filtering and processing fastq files, so reviewing its help and documentation is highly recommended for maximizing its utility..

Fastp produces a report detailing input file information and the impact of quality filtering, useful for review or further analysis with tools like MultiQC.
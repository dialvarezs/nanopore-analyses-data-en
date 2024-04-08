---
title: 3. Quality control and filters
---
>[!summary] In this chapter
> - How to perform quality control using tools such as FastQC and NanoPlot and understand the results provided by these tools.
> - How to summarize statistics for a set of samples.
> - How to filter your data using tools like Fastp and Cutadapt.

## Quality control
The quality check process is a critical step when analyzing raw data, so it is quite important to perform quality control and filter out poor quality reads and remove adapters, etc before starting the analyses.

#### Quality
All the `fastq` files contains the quality data encoded in ASCII format.

![[fq.png]]

To understand ASCII encoding the following table showing the equivalence between ASCII symbols and quality score can be displayed.

![[illumina_fastq_coding.png]]

To calculate the probability that the base is incorrect, the formula `10^(-Q/10)` is used.

| Phread Quality Score | Probabildad de una base incorrecta | Exactitud |
| -------------------- | ---------------------------------- | --------- |
| 10                   | 1 en 10                            | 90%       |
| 20                   | 1 en 100                           | 99%       |
| 30                   | 1 en 1000                          | 99,9%     |
| 40                   | 1 en 10000                         | 99,99%    |

#### FastQC
[Repositorio](https://github.com/s-andrews/FastQC) || [Página oficial](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/) || [Documentación](https://dnacore.missouri.edu/PDF/FastQC_Manual.pdf)
FastQC is a tool that allows to obtain quality metrics from fastq files easily. It can accept input files in `.fastq`, `.bam` or `.sam` formats. After running FastQC, an HTML file is generated where each metric is evaluated as a module with states marked as Approved / Fail / Warning (depending on the quality).  This states are designed to evaluate files from second-generation sequencing (high quality data) and Whole Genome Sequencing. Therefore, it is common to encounter failed modules when analyzing Nanopore data. For this reason, it is important to understand the type of data you are working with and evaluate the modules accordingly.

FastQC have the following modules:
- **Basic statistics:** Basic information from the file (number of reads, min and max length, GC content
- **Per Base Sequence Quality:** In x-axis the position in the read is displayed, Y-axis show the quality score.
	Aditionally, the boxplot shows:
	- The central red line is the median value
	- The yellow box represents the inter-quartile range (25-75%)
	- The upper and lower whiskers represent the 10% and 90% points
	- The blue line represents the mean quality
	
	- A warning will be issued if the lower quartile for any base is less than 10, or if the median for any base is less than 25.
	- This module will raise a failure if the lower quartile for any base is less than 5 or if the median for any base is less than 20.
	
* **Per Sequence Quality Scores**: Number of reads per quality score, in x-axis quality is displayed, in y-axis number of reads.
	* A warning is raised if the most frequently observed mean quality is below 27 - this equates to a 0.2% error rate.
	* An error is raised if the most frequently observed mean quality is below 20 - this equates to a 1% error rate.

* **Per Base Sequence Content** The proportion of each four normal DNA base along the read is showed. Each color represented one base (A/C/G/T), indicated in the legend of the plot.

* **Per Sequence GC Content:** GC content across the whole length of each sequence (x-axis represented GC content and y-axis represented number of sequences).

* **Contenido de N's por base:** Percentage of base calls at each position for which an N was called.

* **Sequence Length Distribution:** Distribution of fragment sizes.

* **Duplicate Sequences:** Degree of duplication for every sequence in a library. The plot shows the relative number of sequences with different degrees of duplication.  A low level of duplication may indicate a very high level of coverage of the target sequence, but a high level of duplication is more likely to indicate some kind of enrichment bias
			- Only  the first 100,000 sequences in each file are analysed and any reads over 75bp in length are truncated to 50bp for the purposes of this analysis.
		 
* **Overrepresented Sequences:** This module lists all of the sequence which make up more than 0.1% of the total. For each overrepresented sequence the program will look for matches in a database of common contaminants and will report the best hit it finds.
			- Only  the first 100,000 sequences in each file are analysed and any reads over 75bp in length are truncated to 50bp for the purposes of this analysis.

* **Adapter Content:** Shows a cumulative percentage count of the proportion of your library which has seen each of the adapter sequences at each position. FastQC included a set of adapters from Illumina, Nextera y Solid.

FastQC can be installed vía conda:
```bash
conda install -c bioconda fastqc
```

To verify that the tool has been installed correctly, you can view the version of the tool and its help:
```bash
fastqc --version
fastqc --help
```

Running FastQC only requires the input file, but you can also specify different parameters such as the number of threads to use and the directory where to save the ouput.  The ouput directory must be created before running the command.



```bash
fastqc <file.fastq>
fastqc <file.fastq> --outdir <fastqc_report> --threads 4
```


Several parameters can be specified, such as the minimun length of the reads to use `--min_length`, specifying a fasta file with contaminants  (`--contaminants`) or adapters (`--adapters`), etc. All the parameters are defined in the help section of the tool.

FastQC has an interface where the file can be uploaded. The output will be displayed in the same interface.

#### Nanoplot
[Repositorie](https://github.com/wdecoster/NanoPlot) 

Nanoplot allows you to create quality report from long read data. There are a online version, but is limitated only a maximum file size of 100MB ([Nanoplot](http://nanoplot.bioinf.be)).

Can be install via  conda
```bash
conda install -c bioconda nanoplot
```

To verify that the tool has been installed correctly, you can view the version of the tool and its help:
```bash
NanoPlot --help
NanoPlot --version
```

A basic running of Nanoplot only requiere the input file, but you can specify the number of threads, and the directory where to save the ouput (if ouput directory don't be specified, the output files will save in the working directory).
```bash
NanoPlot --fastq bc02.fq --outdir bc02 
```

The minimum and maximum length of the reads to used can be specified too. Also a prefix to use in the output files can be specified (important if you use MultiQC to summarize reports).

```bash
NanoPlot --threads 4 --fastq bc02.fq --outdir bc02 --minlength 1000 --maxlength 2000 --prefix bc02
```

NanoPlot generate a HTML report with interactive plots and a text file sumarized report. 

#### MultiQC
[Documentación](https://multiqc.info/docs) 

MultiQC allows you to join results from different bioinformatics tools across many samples into a single report. MultiQC searches a given directory for analysis logs and compiles a HTML report.

MultiQC  supports logs file from `fastqc`, `fastp`, `cutadapt`, `NanoPlot`, `jellyfish`, and several tools. All modules availables in the official documentation ([MultiQC modules](https://multiqc.info/docs/#multiqc-modules)). 

Can be install via  conda and verify that the tool has been installed
```bash
conda install -c bioconda multiqc

multiqc --help
multiqc --version
```

Examples of running:
```bash
multiqc <path_to_fastqc_results>
multiqc <path_to_NanoStats_out.txt> --module nanostat
multiqc <path_to_fastqc_results> --filname multiqc_raw_data
```


## Filtros de calidad
#### Fastp
[Repository](https://github.com/OpenGene/fastp)

Fastp is a tool for processing fastq format files. It allows for adapter trimming, quality filtering, trimming by quality or specific positions, filtering reads by minimum and maximum length, among other features.

Fastp can be installed via conda, and you can check its version and help options: 
```bash
conda install -c bioconda fastp

fastp --help
fastp --verson
```

Fastp can handle files from single-end sequencing or accept both files for pair-end sequencing. Here are two simple examples of executing with default parameters for different sequencing types:
```bash
fastp -i seq.fastq -o seq_filtered.fastq  # Single end data
fastp --in1 in.R1.fq.gz --in2 in.R2.fq.gz --out1 out.R1.fq.gz --out2 out.R2.fq.gz  # Pair end data
```
The above example would use Fastp's default filters (suitable quality of Q15, adapter detection for Illumina, minimum size of 15bp, etc.), so it's important to ensure that the default filters align with the desired filters for your data.

By default, Fastp uses a read filtering method that determines if a read passes based on whether a certain percentage of its bases meet a specific quality level. Normally, 60% of the bases must be equal to or above quality Q15. However, you can switch to a method based on the average quality of the read (using the `-e` option).

Some parameters to consider when using Fastp:
- `--disable_quality_filtering (-Q)`: Disables Fastp's default quality filter.
- `--qualified_quality_phred (-q)`: Specifies the minimum quality for a base to be considered high-quality (default is 15).
- `--average_qual (-e)`: Minimum average quality for a sequence to be considered suitable.
- `--unqualified_percent_limit (-u)`: Allowed percentage of bases that do not meet the quality threshold (default is 40%).
- `--disable_adapter_trimming (-A)`: Disable default adapter filtering (for Illumina's Paired End and Single End sequencing).
- `--adapter_fasta`: If default adapter filtering is disabled, you can specify a file containing your sequencing adapters to be removed.
- `--length_required`: Allows setting a minimum length and discarding reads shorter than this (default is 15bp).
- `--length_limit`: Allows setting a maximum length, discarding reads longer than this.
- `--cut_front`: Remove bases at the start of the sequence (5') that have an average quality lower than specified with `cut_mean_quality`. A 4bp window is used to calculate average quality (modifiable via `cut_window_size`).
- `--cut_tail`: Remove bases at the end of the sequence (3') that have an average quality lower than specified with `cut_mean_quality`.
- `--cut_mean_quality`: Minimum quality used for trimming the ends of the sequence for average quality (when using parameters like `cut_front`, `cut_tail`) (default is Q20).
- `--trim_front`: Trim a fixed number of bases from the start of the sequence (5').
- `--trim_tail`: Trim a fixed number of bases from the end of the sequence (3').
- `--max_len`: If the read is longer than the specified size, trim the remaining bases at 3'.
- `--disable_trim_poly_g`: By default, Fastp removes polyG tails it finds; this option allows disabling this feature.

Below are examples of executing with custom parameters:
```bash
fastp -i <archivo_fastq> -o <arhivo_output> --html fastp_report.html \
  --disable_trim_poly_g --disable_adapter_trimming \
  --thread <número_de_cores> -q <calidad_mínima> \
  --length_required <tamaño_mínimo> \ 
  --length_limit <tamaño_máximo> \
  --cut_front \
  --cut_tail \
  --cut_mean_quality <calidad_minima>

fastp -i input.fastq -o output.fastq --html fastp_report.html \
  --disable_trim_poly_g --disable_adapter_trimming \
  --thread 6 -q 15 \
  --length_required 1000 \ 
  --length_limit 2000 \
  --cut_front \
  --cut_tail \
  --cut_mean_quality 12
```

It's worth noting that `fastp` offers many more options for filtering and processing fastq files, so reviewing its help or documentation can be very beneficial.

Fastp generates a report summarizing the general information of the input file and relevant information before and after applying quality filters. This information can be aggregated through MultiQC using the module designed to work with files from Fastp (`fastp`).
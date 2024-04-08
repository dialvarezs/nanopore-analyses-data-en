---
title: 2. Sequencing and data analysis options
---
> [!summary] In this chapter
> - Considerations when working with Oxford Nanopore
> - Data analysis options
> - Sequencing output structure
> - What is the basecalling and how accurate it is


## Oxford Nanopore Sequencing experiment design and analysis considerations

>[!info] Sequencing related units
> - 1 Kb (kilobase) = 1 000 pb
> - 1 Mb (megabase) = 1 000 kb or 1 000 000 pb
> - 1 Gb (gigabase) = 1 000 Mb or 1 000 000 000 pb

### Coverage and depth

**Coverage** is typically used to measure how many times a specific region, such as a gene or an entire genome, has been sequenced. However, it does not necessarily indicate uniform coverage across every position within the target region; rather, it represents an average.

For instance, having a coverage of 50x means that, on average, each position is covered by 50 reads. If the length of the region is 10 Mb, then the total amount of information should be around 500 Mb.

The term **depth** is closely related and often refers to the number of reads covering a particular region. For example, when discussing genetic variants, it is common to report the associated depth.

In some less frequent cases, coverage may denote the percentage of the target region that has been sequenced. This occurs when it's not possible to sequence the entire region or when the required depth for subsequent analyses is not met.

The following diagram graphically illustrates the concepts explained above, depicting the distribution of reads across positions within the target region.

![[coverage.png]]

Depending on the type of analysis intended and its purpose (exploratory, research, or clinical), there may be different coverage requirements. The precision achieved for each analysis using different sequencing platforms is usually documented by the manufacturer or previous research.

It is important to determine the required depth before conducting an experiment, as this will help decide how many libraries to sequence to achieve the goal. Similarly, it is relevant to consider the quality of the sequenced information, as a significant amount of information can be lost during quality control stages, which will affect the actual coverage.

### Flowcell efficiency

Oxford Nanopore offers different equipment and flow cells that provide varying amounts of data, so it is important to consider the amount of gigabases each device can deliver when designing an experiment.
- Flongle: 126 channels, maximum throughput of 2.6Gb.
- MinION: 512 channels, maximum throughput of 50Gb, con 512 canales. Nanopore indicates that this flowcell is suitable for experiments between 10Gb and 20Gb.
- PromethION: 2675 channels, maximum throughput of 290Gb.

In every experiment, it is common to lose some portion of the sequenced information due to quality filtering and demultiplexing steps, so it's important to take this into account when designing experiments.

### Sequence content

Depending on the experiment's design, the generated sequence may contain different elements.
- Primer: if it's an amplicon amplified by PCR.
- Barcode: if it's multiple samples in a single sequencing run.
- Adapter: in all cases.
The tool used for basecalling and demultiplexing generally can remove these elements from the sequence, leaving only the region of interest.

![[amplicon.jpg]]

### Oxford Nanopore accuracy

Sequencing accuracy is crucial because it determines the amount of data required to achieve the desired precision in the desired type of analysis, as well as how many samples can be processed in a single experiment.
#### RAW Data

Utilizing the latest V14 chemistry and SUP basecalling algorithms, Nanopore technology can achieve raw data accuracy up to Q23 with simplex sequencing and Q30 with duplex sequencing.

| Flowcell | Kit                         | Basecalling        | Raw read accuracy |
| -------- | --------------------------- | ------------------ | ----------------- |
| R10.4.1  | Ligation Sequencing Kit V14 | HAC basecalling    | 99.0% (Q20)       |
| R10.4.1  | Ligation Sequencing Kit V14 | SUP basecalling    | 99.5% (Q23)       |
| R10.4.1  | Ligation Sequencing Kit V14 | Duplex basecalling | >99.9% (Q30)      |

#### Consensus

Consensus is achieved by assembling multiple reads, thereby reducing errors through overlapping.

| Flowcell | Consensus precision | Analysis tools                                          |
| -------- | ------------------- | ------------------------------------------------------- |
| R10.4.1  | Q50 at 10-20x       | Basecalling sup + assembly with Flye + medaka polishing |

For most up-to-date information regarding Nanopore accuracy, visit [Oxford Nanopore Technologies' website](https://nanoporetech.com/platform/accuracy).

## Data analysis options

Analyzing sequencing data efficiently requires using bioinformatics tools systematically, particularly for complex tasks or when handling multiple samples. While executing these tools individually is possible, the integration of bioinformatics pipelines offers a more efficient solution. These pipelines streamline the workflow by coordinating the execution of various tools, thereby enhancing the efficiency, consistency, and reproducibility of data analysis.
### EPI2ME

[Official page](https://epi2me.nanoporetech.com/workflows) (_requires authentication_)

EPI2ME is Oxford Nanopore's analysis platform, and its major advantage is having pre-designed workflows ready to be executed, in addition to operating on the cloud. This means that users can execute workflows without needing substantial resources on their own computers since the programs run in the cloud and not on the user's device.

EPI2ME has various pipelines for different analyses. More information about the available workflows can be found [here](https://labs.epi2me.io/wfindex). Some of the available workflows include:

- RAW data processing and quality control
    - Basecalling and demultiplexing
    - Obtaining basic RNA and DNA data statistics (sequence size, quality score, amount of data generated)
- Alignments:
    - Alignment of reads with the human genome (GRCh38)
    - Alignment of reads with a reference
- Variant Calling:
    - Detection of structural variants (insertions, deletions, duplications)
- Other workflows:
    - Assembly and identification of SARS-CoV-2 with ARTIC
    - Assembly and alignment of reads with reference for Monkeypox
    - Taxonomic identification of 16S rRNA genes with Blast

### Bioinformatics pipelines

Bioinformatics pipelines are a prevalent method for executing complex bioinformatics workflows. These pipelines are structured sequences of computational processes, interconnected so that the output files from certain tools serve as inputs for others.

Bioinformatics pipelines are essential for automating and ensuring streamlined, reproducible analyses. This is accomplished by specifying the execution order of tools, how they will be executed, and the handling of their outputs (whether to store them, use them as input for another tool, transform them, etc.).

There are several tools available for defining and executing workflows, such as [Nextflow](https://www.nextflow.io/), [Snakemake](https://snakemake.readthedocs.io/en/stable/) o [Cromwell](https://cromwell.readthedocs.io/en/stable/). These tools provide the syntax for defining and developing workflows and enable their execution across different computational environments. Developing new pipelines might require programming skills for integration steps and/or post-processing tasks.

![[basic-workflow-bg2.svg]]

Numerous bioinformatics pipelines are available online, offering comprehensive analysis tools for a variety of research needs. Repositories such as [EPI2ME Labs Workflows](https://labs.epi2me.io/wfindex/) and [Nf-core](https://nf-co.re/) provide standardized pipelines tailored for different analyses, ensuring quality and reproducibility. These collections are essential resources for researchers seeking reliable and tested workflows. In addition to these repositories, independent pipelines such as [Bactopia](https://github.com/bactopia/bactopia) cater to specific needs, in this case, bacterial genomic analyses.
## Basecalling and demultiplexing

In Oxford Nanopore's sequencing platform, DNA molecules pass through nanopores. As the molecule passes through a pore, it can detect alterations caused by the molecule's ions, which are encoded by an electrical signal that is recorded on the computational equipment. Determining the nucleotide sequence of the molecule that passed through the pore requires the use of complex computational algorithms; this process is known as **basecalling**.

![[MinION_GIF_01.webm]]

Throughout the development of this technology, multiple changes have been introduced to enhance the precision of base recognition, regarding both the chemistry of flow cells and the software that performs basecalling. One of the most recent changes is the introduction of the "R10" nanopore, which features two readers instead of one, aiding in the resolution, especially of homopolymeric regions.

([Dorado](https://github.com/nanoporetech/dorado)) is the official tool created by Oxford Nanopore for basecalling, utilizing a neural network-based algorithm that is trained with mixed datasets of both amplified and native DNA/RNA from various organisms (plants, animals, bacteria, viruses). Dorado also includes features for filtering out low-quality reads, recognizing and trimming known adapters, and estimating methylation. And finally, Dorado can demultiplex samples using Nanopore barcoding system.

#### Basecalling accuracy

Dorado features multiple models with different balances between basecalling accuracy and required computational capacity. Specifically, the available models are:

| Name                   | Simplex precission | Computational requirements                                   |
| ---------------------- | ------------------ | ------------------------------------------------------------ |
| fast [`fast`]          | 95.5%              |                                                              |
| high accuracy [`hac`]  | 99%                | between 5 and 8 times more computationally intense than fast |
| super accuracy [`sup`] | 99.5%              | 3 times more computationally intense than high accuracy      |

![[nanopore_accuracy.png]]

While the advantage of models with higher accuracy is clear, their greater hardware requirements limit their use to systems with more power or a longer runtime. In particular, the `sup` model has such a high computational cost that it becomes impractical to use without dedicated hardware (GPU). Basecalling can be performed in real-time while sequencing the sample (since MinKNOW integrates Dorado), however, `sup` is only selectable is the computer has a comptatible GPU, and using `hac` can result in the basecalling process continuing for multiple days after the experiment ends if appropriate hardware is not available.

#### Sequencing Output

When sequencing a sample, MinKNOW generates multiple files that include the sequenced information and log files. The most relevant files are the `.pod5` files, which contain the variations in voltage recorded by the pore as the DNA strand passes through. These files can also contain the result of the basecalling if this option was enabled in MinKNOW.

Within the sequencing directory, other files can also be found:
- `barcode_alignment`: If the basecalling and demultiplexing option is selected, this file will save the information associated with each read and the detected barcodes.
- `final_summary`: Contains summarized information of the sequencing, such as the flow cell and kit used, number of files processed, among others.
- `other_reports/`: Reports with technical details of the sequencing.
- `throughput`: Contains information per minute of the number of bases sequenced, number of reads that have been basecalled (both fail and pass).
- `fast5/`: Directory that contains the fast5 files obtained in the sequencing.
- `fastq_pass/` and `fastq_fail`: If the basecalling option is selected, these directories will store the fastq files of those reads that meet and don't meet the minimum quality criteria (by default 4,000 reads per file). Enabling basecalling also will separate `pod5` files in pass and fail. If barcoding was enabled, this directories with have subfolders containing the reads associated with each barcode.
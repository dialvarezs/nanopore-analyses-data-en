Temas a abordar:
- Como realizar un control de calidad con herramientas como Fastqc y Nanoplot y entender los resultados que entregan las herramientas.
- Como resumir las estadísticas para un conjunto de muestras
- Como filtrar nuestros datos con herramientas como Fastp y Cutadapt

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

* **# Sequence Length Distribution:** Distribution of fragment sizes.

* **Duplicate Sequences:** Degree of duplication for every sequence in a library. The plot shows the relative number of sequences with different degrees of duplication.  A low level of duplication may indicate a very high level of coverage of the target sequence, but a high level of duplication is more likely to indicate some kind of enrichment bias
			- Only  the first 100,000 sequences in each file are analysed and any reads over 75bp in length are truncated to 50bp for the purposes of this analysis.
		 
* **Overrepresented Sequences:** This module lists all of the sequence which make up more than 0.1% of the total. For each overrepresented sequence the program will look for matches in a database of common contaminants and will report the best hit it finds.
			- Only  the first 100,000 sequences in each file are analysed and any reads over 75bp in length are truncated to 50bp for the purposes of this analysis.

* **# Adapter Content:** Shows a cumulative percentage count of the proportion of your library which has seen each of the adapter sequences at each position. FastQC included a set of adapters from Illumina, Nextera y Solid.

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
[Repositorio oficial](https://github.com/OpenGene/fastp)

Fastp es una herramienta para procesar archivos en formato fastq. Permite recortar adaptadores, filtrar por calidad, recortar extremos por calidad o por posición determinada, filtrar lecturas por largo mínimo y  máximo, entre otras características.

Se puede instalar fastp vía conda y verificar su versión y ayuda:  
```bash
conda install -c bioconda fastp

fastp --help
fastp --verson
```

Se puede indicar a fastp archivos provenientes de secuenciaciones Single end, o también pasarle ambos archivos al utilizar secuenciación Pair End. Dos ejemplos sencillos de ejecución utilizando los parámetros por defecto con diferentes tipos de secuenciaciones sería:
```bash
fastp -i seq.fastq -o seq_filtered.fastq  # Single end data
fastp --in1 in.R1.fq.gz --in2 in.R2.fq.gz --out1 out.R1.fq.gz --out2 out.R2.fq.gz  # Pair end data
```
El ejemplo anterior utilizaría los filtros por defecto de Fastp (calidad apta de Q15, detectar adaptadores para Illumina, tamaño mínimo de 15pb, etc.), por lo que hay que tener cuidado y verificar que los filtros por defecto sean acorde a los filtros que queremos realizarle a nuestros datos.

Fastp utiliza por defecto un método de filtrado de reads en el que se determina si un read pasa o no el filtro considerando si un determinado porcentaje de sus bases cumple con cierta calidad. Por defecto, se requiere que un 60% de las bases posean calidad igual a superior a Q15.
Igualmente, es posible cambiar el método a uno basado en la calidad media del read (con la opción `-e`).

Algunos parámetros a tener en consideración a la hora de utilizar Fastp:
* `--disable_quality_filtering (-Q)`: Deshabilita el filtro por defecto de calidad de fastp.
* `--qualified_quality_phred (-q)`: Para indicar la calidad mínima para que una base se considere con calidad apropiada (por defecto 15).
* `--average_qual (-e)`: Calidad mínima promedio para que una secuencia sea considerada como apta.
* `--unqualified_percent_limit (-u)`: Porcentaje permitido de bases que no pasen el corte de calidad (por defecto 40%).
* `--disable_adapter_trimming (-A)`: Desactivar filtro de adaptadores por defecto (adaptadores de secuenciaciones Paired End y Single End de Illumina).
* `--adapter_fasta`: En caso de deshabilitar el filtro de adaptadores por defecto, se puede indicar un archivo que posea los adaptadores de nuestra secuenciación y pedirle que los elimine.
* `--length_required`: Se puede indicar un largo mínimo y descartar todas aquellas lecturas que tengan un tamaño menor al indicado (por defecto es 15bp).
* `--length_limit`: Se puede indicar un largo máximo, y descartar todas aquellas lecturas que tengan un tamaño mayor al indicado.
* `--cut_front`: Eliminar las bases al comienzo de la secuencia (5') que tengan una calidad promedio menor a la indicada con ` cut_mean_quality`. Se utiliza una ventana de 4pb para hacer los cálculos de calidad promedio (modificable mediante `cut_window_size`).
* `--cut_tail`: Eliminar las bases al final de la secuencia (3') que tengan una calidad promedio menor a la indicada con ` cut_mean_quality`.
* `--cut_mean_quality`: Calidad mínima utilizada para recortar los extremos de la secuencia por calidad promedio (al utilizar parámetros como `cut_front`, `cut_tail`) (por defecto Q20).
* `--trim_front`: Recortar una cantidad fija de bases al comienzo de la secuencia (5').
* `--trim_tail`: Recortar una cantidad fija de bases al final de la secuencia (3').
* `--max_len`: Si la lectura es mayor al tamaño indicado, recortar las bases restantes en 3'.
* `--disable_trim_poly_g`: Por defecto fastp elimina las colas polyG que encuentre, esta opción permite deshabilitar esta caracteristica.

A continuación se muestran ejemplos de ejecución utilizando parámetros personalizados: 
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

Cabe destacar que `fastp` cuenta con muchas opciones más para realizar filtros y procesar archivos fastq, por lo que revisar su ayuda o documentación será de gran utilidad. 

Fastp entrega como resultado un reporte con la información general del archivo utilizado como input, e información relevante antes de realizar los filtros de calidad y luego de realizar los filtros de calidad. Esta información puede resumirse a través de MultiQC utilizando el módulo diseñado para trabajar con archivos provenientes de Fastp (`fastp`).

Tarea: Realizar control de calidad y filtros de calidad a datos de genoma completo. Se espera obtener los siguientes reportes:
	- Reporte `multiqc` obtenido con `fastqc` (antes y después de filtros)
	- Reporte `multiqc` obtenido con `NanoPlot` (antes y después de filtros)
	 - Reporte `multiqc` de filtrado con `fastp` 
	- Calcular la profundidad post filtros
	- Indicar si los datos son adecuados para realizar ensamblaje de genoma (justificar brevemente)
	- Reporte técnico con detalles de las versiones de las herramientas, filtros aplicados y comandos utilizados.
	- Idealmente juntar los 3 reportes de calidad en un solo reporte final de `multiqc`.



* Considerar que las secuencias no poseen adaptadores.
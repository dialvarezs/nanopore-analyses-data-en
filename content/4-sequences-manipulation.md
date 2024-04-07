Temas a abordar:
- Manipulación de archivos procedentes de `raw data` (`fasta`, `fastq`) o de ensamblajes con `Seqkit`.
- Obtención de información taxonomica con `Taxonkit`

#### seqkit
[Repositorio](https://github.com/shenwei356/seqkit) || [Documentación](https://bioinf.shenwei.me/seqkit/usage/)

Seqkit permite la manipulación y procesamiento de archivos FASTQ y FASTA de manera eficiente.

Se puede instalar vía conda y verificar su versión y ayuda.
```bash
conda install -c bioconda seqkit
```

- Convertir archivo fastq a formato fasta
```bash
seqkit fq2fa sequences.fq -o sequences.fa
```

- Convertir un archivo fastq multilínea a formato fastq de 4 líneas
```bash
seqkit seq reads_1.fq.gz -w 0
```

- Obtener el reverso complementario
 ```bash
 seqkit seq hairpin.fa.gz --reverse --complement
```

- Eliminar gaps dentro de las secuencias
```bash
seqkit seq --remove-gaps
```

- Filtrar secuencias por tamaño
```bash
seqkit seq sequences.fa --min-len 100 --max-len 1000
seqkit seq sequences.fa --min-len 100 --max-len 1000
```

- Obtener estadísticas básicas de las secuencias (cantidad de secuencias, tamaño mínimo y máximo, promedio de longitud, etc). Se pueden obtener las estadisticas de varios archivos a la vez.
```bash
seqkit stats sequences.fa
seqkit stats *.f{a,q}.gz
```

- Obtener un histograma de los tamaños de las secuencias o calidad promedio
```bash
seqkit watch --fields ReadLen sequences.fq.gz -O histogram_len.png
seqkit watch --fields MeanQual sequences.fq.gz -O histogram_quality.png

```
	- Campos disponibles para realizar el histograma: ReadLen, MeanQual, GC, GCSkew.

- Traducir secuencias a proteina
```bash
seqkit translate c.fna
```

- Imprimir el nombre de las secuencias, o su identificador solamente
```bash
seqkit seq hairpin.fa.gz --name
seqkit seq hairpin.fa.gz --name --only-id
```

- Convertir RNA a DNA
```bash
seqkit seq sequences.fa --rna2dna
```

- Filtrar secuencias por id
```
 seqkit grep -p "d9321e7b-3969-4ecd-9bb5-e85d4c59d86f" sequences.fa
 seqkit grep -f ids_to_filter.txt sequences.fa
```
	Se puede utilizar la opción `-v` para realizar la búsqueda inversa, es decir, seleccionar todas aquellas lecturas que no esten en la lista indicada.
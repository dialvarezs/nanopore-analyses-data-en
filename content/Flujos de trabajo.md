
## Estrategias de análisis de datos de secuenciación

### Pipeline bioinformático

Un pipeline es el diseño de un proceso que involucra la ejecución de múltiples herramientas, las cuales son "interconectadas" a través de la utilización de archivos de salidas de algunas herramientas como las entradas de otras.

Los pipelines son de gran ayuda a la hora de automatizar y garantizar la reproducibilidad de los análisis bioinformáticos. Esto se logra mediante la especificación de cómo se ejecutarán las herramientas, en qué orden, y que se hará con sus resultados (almacenar, utilizarlo como entrada para otra herramienta, transformarlo, etc).

Existen diferentes herramientas para la definición y ejecución de flujos de trabajos, como [Nextflow](https://www.nextflow.io/), [Snakemake](https://snakemake.readthedocs.io/en/stable/) o [Cromwell](https://cromwell.readthedocs.io/en/stable/), que establecen la sintaxis para definir y desarrollar flujos de trabajos, y la ejecución de éstos en diferentes entornos computacionales. El desarrollo de nuevos pipelines puede requerir el manejo de lenguajes de programación para pasos de integración y/o post procesamiento, por lo que su desarrollo no será abordado en este curso.

![[basic-workflow-bg2.svg]]


### Ejemplos de pipelines

#### Pipeline de ensamblaje
Flujo de trabajo utilizado para el ensamblaje y anotación de genomas de bacteriófagos, pero es aplicable para ensamblajes de genomas de otras especies también. 
![[phage_assembly_pipeline_ont.svg|500]]

#### Pipeline de asignación taxónomica
Este pipeline se utiliza para caracterización de comunidades microbianas y evaluación de diferencias entre grupos muestrales. También se incluye predicción de vías metabólicas, análisis estadísticos y generación de gráficas asociadas.
![[16s.jpg|700]]

#### Pipeline de detección de clones en repertorio inmune
Este pipeline se utiliza para la identificación de clones en repertorios inmunes de nuestras de pacientes de leucemia linfática crónica y evaluación de homlogía con el gen V. También se incluye análisis de repertorio clonal (uso de genes, diversidad, etc).
![[repertorio.jpg|700]]

### Post análisis a través lenguajes de programación

Habitualmente, los datos generados por pipelines o diferentes herramientas se procesan con algún lenguaje de programación para limpiar la información y obtener resultados derivados como gráficos o tablas.

En muchos lenguajes de programación existen librerías que permiten leer, procesar y escribir datos genómicos, algunos ejemplos son el paquete `biopython` para el lenguaje de programación `Python` o la gran cantidad de librerías disponibles en el repositorio `Bioconductor` para `R`.

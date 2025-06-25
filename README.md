# Semana 13: Análisis metataxonómico utilizando datos de secuenciación de Nanopore

## Logro de la sesión: 

Al finalizar la sesión, el estudiante conoce y utiliza herramientas bioinformáticas para realizar un análisis metataxonómico a partir de los datos de secuenciación Nanopore.

# Estructura de la práctica:

1. Acceso al servidor de cómputo
2. Análisis de calidad del secuenciamiento Nanopore
3. Limpieza de los archivos FASTQ 
4. Identificación del perfil taxonómico a partir de los archivos FASTQ
5. Obtención del archivo BIOM
6. Análisis de diversidad  

## Programas requeridos:

### Programas de acceso al servidor:

PuTTY v0.79 https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html
   - **Descripción:** PuTTY es un cliente SSH, Telnet y Rlogin gratuito y de código abierto para Windows y sistemas Unix. Se utiliza principalmente para establecer conexiones seguras de línea de comandos a servidores remotos.

WinSCP v6.1 https://winscp.net/eng/download.php
   - **Descripción:** WinSCP es un cliente SFTP, FTP, WebDAV, Amazon S3 y SCP gratuito y de código abierto para Windows. Permite la transferencia segura de archivos entre un ordenador local y servidores remotos mediante una interfaz gráfica de usuario. 

### Programas bioinformáticos:

### Programas bioinformáticos:

Emu v3.5.1 https://github.com/treangenlab/emu
   - **Descripción:** Emu (Evolutionary Microbial Unification) es una herramienta avanzada para la cuantificación precisa de microorganismos, optimizada para datos de secuenciación completa del gen 16S rRNA. Utiliza algoritmos sofisticados para asignar lecturas de forma precisa, incluso con errores, lo que permite una identificación fiable a nivel de especie. Es ideal para desentrañar la composición de comunidades microbianas complejas usando datos de 16S, especialmente de tecnologías de lectura larga.

MultiQC v1.28.0 https://multiqc.info
   - **Descripción:** MultiQC es una herramienta que agrega informes de control de calidad de múltiples herramientas de análisis bioinformático en un único informe HTML interactivo. Es compatible con una amplia gama de herramientas, incluyendo FastQC, NanoPlot, PycoQC y Trim Galore!, facilitando la revisión y comparación de los resultados de control de calidad de múltiples muestras.

Nanofilt v2.8.0 https://github.com/wdecoster/nanofilt
   - **Descripción:** NanoFilt es una herramienta para filtrar datos de secuenciación de Nanopore basándose en la calidad y la longitud de los reads. Permite seleccionar reads de alta calidad para análisis posteriores.

NanoPlot v1.41.6 https://github.com/wdecoster/NanoPlot
   - **Descripción:** NanoPlot es una herramienta para la visualización de datos de secuenciación de Nanopore. Genera varios tipos de gráficos para evaluar la calidad y las características de los reads, como la distribución de longitudes y la calidad a lo largo de los reads.

### Herramientas bioinformáticas en línea:

MicrobiomeAnalyst v2.0 https://www.microbiomeanalyst.ca/MicrobiomeAnalyst/home.xhtml
   - **Descripción:** MicrobiomeAnalyst es una plataforma web fácil de usar para el análisis estadístico, visual y meta-análisis integral de datos de microbioma. Está diseñada para ser accesible a investigadores y clínicos con diversos niveles de experiencia en bioinformática, proporcionando una interfaz intuitiva para explorar una amplia variedad de métodos establecidos. MicrobiomeAnalyst soporta datos de genes marcadores (por ejemplo, 16S rRNA) y datos de metagenómica de escopeta (shotgun). Sus módulos permiten el perfilado de la comunidad, análisis comparativos, predicción funcional e integración con conjuntos de datos públicos o firmas microbianas conocidas. Las funcionalidades clave incluyen el procesamiento y la normalización de datos, diversos análisis estadísticos (por ejemplo, abundancia diferencial), visualizaciones interactivas (por ejemplo, gráficos PCoA, mapas de calor, redes de correlación) e interpretación funcional (por ejemplo, análisis de redes metabólicas). También ofrece un módulo de procesamiento de datos brutos para datos de amplicones y un módulo para integrar datos de microbioma y metabolómica.

## Metodología:

## 1.	Acceso al servidor de cómputo:

### Abrir el programa PuTTY, colocar el hostname ( 10.142.250.66 ) y port ( 22 ), y dar clic en Open:

<img width="500" alt="image" src="https://github.com/user-attachments/assets/92f89dbb-1a21-411d-adb5-38fe486a5567" />



### En la terminal abierta, escribir su usuario y contraseña correspondiente para tener acceso al servidor de cómputo Tensor:
 
<img width="700" alt="image" src="https://github.com/user-attachments/assets/4d246e93-c59c-4749-a2dd-03db25c53654" />


### Abrir el programa WinSCP, colocar el hostname ( 10.142.250.66 ) y port ( 22 ), escribir su usuario y contraseña correspondiente para tener acceso al servidor de cómputo Tensor, y hacer clic en Login:

<img width="500" alt="image" src="https://github.com/user-attachments/assets/ef4dc253-ce4a-417d-b761-39692d2a011a" />

<img width="500" alt="image" src="https://github.com/user-attachments/assets/577debca-6085-47c5-9bbd-73688bfa8bb0" />

## 2. Análisis de calidad del secuenciamiento Nanopore

```bash
cd

mkdir metataxonomic

cd metataxonomic

mkdir quality

cd quality

mkdir its

cd its

conda activate quality

NanoPlot -t 5 --fastq /data/2025_1/sequencing/metataxonomica/its_curso/b01.fastq.gz -p b01_its_ --only-report --maxlength 5000 -o .
```

```bash
Repetir el mismo comando para los barcodes b02, b03, b04, b05, b06, b07, b08, b17 y b18
```

```bash
multiqc -o masato_its .
```

## 3. Limpieza de los archivos FASTQ 

```bash
cd ~/metataxonomic

mkdir trim

cd trim

gunzip -c /data/2025_1/sequencing/metataxonomica/its_curso/b01.fastq.gz | NanoFilt -q 10 --length 600 --maxlength 2000 | gzip > b01_its.fastq.gz
```

```bash
# Crear el siguiente script:

nano count.sh

#!/bin/bash

echo "--- Conteo de Lecturas por Muestra (Archivos FASTQ) ---"
echo "Archivo FASTQ | Número de Lecturas"
echo "-----------------------------------"

for file in *.fastq; do
    if [ -f "$file" ]; then
        num_lines=$(wc -l < "$file" | awk '{print $1}')
        num_reads=$(echo "$num_lines / 4" | bc)
        printf "%-15s | %s\n" "$file" "$num_reads"
    fi
done

echo "-----------------------------------"
```

```bash
cd ~/genomics/quality/nanopore

NanoPlot -t 2 --fastq ~/genomics/trimming/nanopore/b15_sup_nanofilt.fastq.gz -p b15_sup_filt_ -o b15_sup_filt --maxlength 100000
```




### 3.1 Crear los archivos metadata.txt y manifest.txt con las siguientes informaciones:

```bash
cd ~/metataxonomic

mkdir illumina

cd illumina

conda activate qiime2-amplicon-2024.10

nano manifest.txt

sample-id	forward-absolute-filepath	reverse-absolute-filepath
01.Chicla  /home/ins_user/metataxonomic/raw_data/illumina/SRR12217142_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217142_2.fastq.gz
02.San_Mateo  /home/ins_user/metataxonomic/raw_data/illumina/SRR12217141_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217141_2.fastq.gz
03.Tamboraque	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217133_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217133_2.fastq.gz
04.Huanchor	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217132_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217132_2.fastq.gz
05.Chacahuaro	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217130_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217130_2.fastq.gz
06.Santa_Eulalia	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217131_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217131_2.fastq.gz
07.Chaclacayo	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217129_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217129_2.fastq.gz
08.Huachipa	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217128_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217128_2.fastq.gz
09.Libertadores	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217127_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217127_2.fastq.gz
10.Nuevo	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217126_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217126_2.fastq.gz
11.Universitaria	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224171_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224171_2.fastq.gz
12.Faucett	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217139_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217139_2.fastq.gz
13.Gambetta	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217138_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217138_2.fastq.gz
14.Chicla	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224180_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224180_2.fastq.gz
15.San_Mateo	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224179_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224179_2.fastq.gz
16.Tamboraque	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224178_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224178_2.fastq.gz
17.Huanchor	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224177_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224177_2.fastq.gz
18.Santa_Eulalia	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224176_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224176_2.fastq.gz
19.Chaclacayo	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224175_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224175_2.fastq.gz
20.Huachipa	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224174_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224174_2.fastq.gz
21.Libertadores	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224173_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224173_2.fastq.gz
22.Nuevo	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224172_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224172_2.fastq.gz
23.Faucett	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217140_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217140_2.fastq.gz
24.Gambetta	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224170_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224170_2.fastq.gz

nano metadata.txt

Sample ID	cuenca	altitud
01.Chicla	Upper_Rimac	3441
02.San_Mateo	Parac-Upper_Rimac	3422
03.Tamboraque	Parac-Upper_Rimac	3023
04.Huanchor	Santa_Eulalia-Parac	2868
05.Chacahuaro	Santa_Eulalia-Parac	2526
06.Santa_Eulalia	Santa_Eulalia	948
07.Chaclacayo	Jicamarca-Santa_Eulalia	657
08.Huachipa	Jicamarca-Santa_Eulalia	396
09.Libertadores	Lower_Rimac	213
10.Nuevo	Lower_Rimac	200
11.Universitaria	Lower_Rimac	69
12.Faucett	Lower_Rimac	33
13.Gambetta	Lower_Rimac	8
14.Chicla	Upper_Rimac	3441
15.San_Mateo	Parac-Upper_Rimac	3422
16.Tamboraque	Parac-Upper_Rimac	3023
17.Huanchor	Santa_Eulalia-Parac	2868
18.Santa_Eulalia	Santa_Eulalia	948
19.Chaclacayo	Jicamarca-Santa_Eulalia	657
20.Huachipa	Jicamarca-Santa_Eulalia	396
21.Libertadores	Lower_Rimac	213
22.Nuevo	Lower_Rimac	200
23.Faucett	Lower_Rimac	33
24.Gambetta	Lower_Rimac	8
```

### 3.2 Importar las lecturas de todas las muestras en archivo demuxed_seqs.qza:

```bash
qiime tools import --type 'SampleData[PairedEndSequencesWithQuality]' --input-path manifest.txt --output-path demuxed_seqs.qza --input-format PairedEndFastqManifestPhred33V2
```

> **Comentario:** Este comando importa los datos de secuencia al formato QIIME 2. El parámetro --type especifica el tipo de datos, --input-path apunta al archivo manifest, --output-path define el nombre del archivo de salida, y --input-format indica el formato de los archivos fastq (lecturas pareadas con codificación de calidad Phred33).

### 3.3 Crear la carpeta visualización y generar el reporte de calidad del archivo demuxed_seqs.qza:

```bash
mkdir visualization

qiime demux summarize --i-data demuxed_seqs.qza --o-visualization visualization/seqs_quality.qzv
```
> **Comentario:** Genera un reporte de calidad de las secuencias demultiplexadas.

### 3.4 Visualizar el archivo creado en https://view.qiime2.org/

> **Comentario:** Este paso se refiere a la visualización de archivos generados por el software QIIME 2. QIIME 2 produce archivos de visualización con la extensión ".qzv", los cuales están diseñados para ser visualizados a través de la plataforma web view.qiime2.org.

### 3.5 Realizar la eliminación de iniciadores en las lecturas y generar el reporte del archivo resultante:

```bash
qiime cutadapt trim-paired --i-demultiplexed-sequences demuxed_seqs.qza --p-cores 2 --p-front-f TCGTCGGCAGCGTCAGATGTGTATAAGAGACAGCCTACGGGNGGCWGCAG --p-front-r GTCTCGTGGGCTCGGAGATGTGTATAAGAGACAGGACTACHVGGGTATCTAATCC --p-minimum-length 100 --p-discard-untrimmed --o-trimmed-sequences demuxed_seqs_trimmed.qza
```

> **Comentario:** Con 'cutadapt', se eliminan los adaptadores y primers de las secuencias. Los parámetros --p-front-f y --p-front-r definen las secuencias a eliminar, --p-minimum-length establece la longitud mínima de las lecturas, y --p-discard-untrimmed descarta secuencias no recortadas.

```bash
qiime demux summarize --i-data demuxed_seqs_trimmed.qza --o-visualization visualization/seqs_quality_trimmed.qzv
```

> **Comentario:** Se genera un nuevo resumen de calidad, esta vez para las secuencias que han sido recortadas, permitiendo evaluar el efecto del recorte en la calidad de los datos.

### 3.6 Realizar la eliminación de ruido (denoising) utilizando DADA2:

```bash
qiime dada2 denoise-paired --i-demultiplexed-seqs demuxed_seqs_trimmed.qza --p-trunc-len-f 0 --p-trunc-len-r 0 --p-n-threads 20 --o-table table.qza --o-representative-sequences representatives.qza --o-denoising-stats denoising_stats.qza
```

> **Comentario:** DADA2 se utiliza para el denoising de las secuencias, corrigiendo errores de secuenciación. Los parámetros --p-trunc-len-f y --p-trunc-len-r se establecen en 0 para no truncar las lecturas, --p-n-threads define el número de hilos a usar, y los parámetros --o- especifican los archivos de salida.

### 3.7 Generar el reporte de los archivos generados:

```bash
qiime feature-table summarize --i-table table.qza --m-sample-metadata-file metadata.txt --o-visualization visualization/table_denoised.qzv
```

> **Comentario:** Se genera un resumen de la tabla de características, que incluye estadísticas sobre la distribución y abundancia de ASVs.

```bash
qiime metadata tabulate --m-input-file denoising_stats.qza --o-visualization visualization/denoising_stats.qzv
```

> **Comentario:** Se tabulan los metadatos de las estadísticas de denoising, proporcionando una visión detallada del proceso de corrección de errores.

```bash
qiime feature-table tabulate-seqs --i-data representatives.qza --o-visualization  visualization/representatives.qzv
```

> **Comentario:** Se tabulan las secuencias representativas, permitiendo la visualización de las secuencias de ASVs identificadas.

### 3.8 Visualizar los archivos creados en https://view.qiime2.org/

### 3.9 Realizar la asignación taxonómica utilizando la base de datos de SILVA:

```bash
qiime feature-classifier classify-sklearn --i-classifier /home/ins_user/metataxonomic/raw_data/silva-138-99-nb-classifier.qza --i-reads representatives.qza --p-n-jobs 1 --o-classification taxa.qza
```

> **Comentario:**  Las secuencias representativas se clasifican taxonómicamente utilizando un clasificador pre-entrenado. El parámetro --i-classifier especifica el clasificador a usar, y --i-reads define el archivo de secuencias.

### 3.10 Generar el reporte de la clasificación taxonómica:

```bash
qiime metadata tabulate --m-input-file taxa.qza --o-visualization visualization/taxa.qzv
```

> **Comentario:** Se tabulan los resultados de la clasificación taxonómica, facilitando la visualización y análisis de la composición taxonómica de las muestras.

### 3.11 Visualizar el archivo creado en https://view.qiime2.org/

### 3.12 Filtrar os ASVs en base a su clasificación taxonómica:

```bash
qiime taxa filter-table --i-table table.qza --i-taxonomy taxa.qza --p-include p__ --p-exclude mitochondria,chloroplast --o-filtered-table table_f.qza
```

> **Comentario:** Se filtra la tabla de caracteristicas excluyendo los ASV clasificados como mitochondria y chloroplast.

```bash
qiime taxa filter-seqs --i-sequences representatives.qza --i-taxonomy taxa.qza --p-include p__ --p-exclude mitochondria,chloroplast --o-filtered-sequences representatives_f.qza
```

> **Comentario:** Se filtra las secuencias representativas excluyendo los ASV clasificados como mitochondria y chloroplast.

### 3.13 Generar el reporte grafico de barras de la clasificación taxonómica:

```bash
qiime taxa barplot --i-table table_f.qza --i-taxonomy taxa.qza --m-metadata-file metadata.txt --o-visualization visualization/taxa_barplot.qzv
```

> **Comentario:** Se genera un gráfico de barras de la composición taxonómica.

### 3.14 Visualizar el archivo creado en https://view.qiime2.org/

### 3.15 Realizar el analisis filogenético de los ASVs:

```bash
qiime phylogeny align-to-tree-mafft-fasttree --i-sequences representatives_f.qza --o-alignment aligned_representative_sequences --o-masked-alignment masked_aligned_representative_sequences --o-tree unrooted_tree --o-rooted-tree rooted_tree
```

> **Comentario:** Se realiza un análisis filogenético de las secuencias representativas filtradas.

### 3.16 Exportar los datos de abundancia y taxonomia:

```bash
qiime tools export --input-path table_f.qza --output-path exported_table
```

> **Comentario:** Se exporta la tabla de características filtrada.

```bash
qiime tools export --input-path taxa.qza --output-path exported_taxonomy
```

> **Comentario:** Se exporta la clasificación taxonómica.

### 3.17 Reemplazar la primera linea del archivo taxonomy.tsv:

```bash
sed -i 's/Feature /#OTU/g' exported_taxonomy/taxonomy.tsv

sed -i 's/Taxon/taxonomy/g' exported_taxonomy/taxonomy.tsv

sed -i 's/Confidence/confidence/g' exported_taxonomy/taxonomy.tsv
```

> **Comentario:** Se formatean los archivos de taxonomía exportados para compatibilidad con otras herramientas.

### 3.18 Generar el archivo BIOM:

```bash
biom add-metadata -i exported_table/feature-table.biom --observation-metadata-fp exported_taxonomy/taxonomy.tsv --sc-separated taxonomy -o feature-table-tax.biom
```

> **Comentario:** Se genera un archivo BIOM que combina la tabla de características filtrada con la información taxonómica. Este archivo es útil para análisis adicionales y visualizaciones.

### 3.19 Exportar los archivos BIOM y metadata.txt

## 4. Análisis de diversidad

### Cargar los archivos en el programa microbiomeanalyst (https://www.microbiomeanalyst.ca/MicrobiomeAnalyst/ModuleView.xhtml) y seguir las indicaciones del docente

# Bitácora de la práctica:

## Resultados

```bash
•	Valores de calidad y numero de lecturas en la data cruda y después de la limpieza 
•	Estadísticas del denoising 
•	Análisis de abundancia a nivel de genero 
•	Análisis de alfa diversidad (2 métricas) a nivel de familia y género 
•	Análisis de beta diversidad (PCoA y NMDS) a nivel de familia y género 
```

## Discusión

```bash
•	Explicar que métrica de la diversidad alfa y que categoria taxonómica representa mejor la diversidad de cada muestra
•	Explicar cual metodo de ordinación de la diversidad beta se correlacina con la similaridad/disimilaridad de las muestras
•	Indicar si hay diferencias con lo reportado en el artículo científico.
```
















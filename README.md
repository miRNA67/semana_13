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

Emu v3.5.1 https://github.com/treangenlab/emu
   - **Descripción:** Emu (Evolutionary Microbial Unification) es una herramienta avanzada para la cuantificación precisa de microorganismos, optimizada para datos de secuenciación completa del gen 16S rRNA. Utiliza algoritmos sofisticados para asignar lecturas de forma precisa, incluso con errores, lo que permite una identificación fiable a nivel de especie. Es ideal para desentrañar la composición de comunidades microbianas complejas usando datos de 16S, especialmente de tecnologías de lectura larga.

MultiQC v1.28.0 https://multiqc.info
   - **Descripción:** MultiQC es una herramienta que agrega informes de control de calidad de múltiples herramientas de análisis bioinformático en un único informe HTML interactivo. Es compatible con una amplia gama de herramientas, incluyendo FastQC, NanoPlot, PycoQC y Trim Galore!, facilitando la revisión y comparación de los resultados de control de calidad de múltiples muestras.

Nanofilt v2.8.0 https://github.com/wdecoster/nanofilt
   - **Descripción:** NanoFilt es una herramienta para filtrar datos de secuenciación de Nanopore basándose en la calidad y la longitud de los reads. Permite seleccionar reads de alta calidad para análisis posteriores.

NanoPlot v1.41.6 https://github.com/wdecoster/NanoPlot
   - **Descripción:** NanoPlot es una herramienta para la visualización de datos de secuenciación de Nanopore. Genera varios tipos de gráficos para evaluar la calidad y las características de los reads, como la distribución de longitudes y la calidad a lo largo de los reads.

Phyloseq v1.46.0 https://github.com/joey711/phyloseq
   - **Descripción:** Phyloseq es un paquete esencial de R que centraliza el análisis y visualización de datos de microbiomas de alta complejidad. Permite integrar tablas de OTUs/ASVs, taxonomía, metadatos y árboles filogenéticos en un solo objeto. Ofrece funciones robustas para filtrado, normalización, cálculo de diversidad y ordenación, facilitando el estudio de comunidades microbianas y promoviendo la reproducibilidad en la investigación.

### Herramientas bioinformáticas en línea:

Easy16S https://shiny.migale.inrae.fr/app/easy16S
   - **Descripción:** Easy16S es una plataforma web Shiny intuitiva para el análisis y visualización de datos de microbioma 16S, construida sobre phyloseq. Permite a usuarios de todos los niveles realizar análisis de diversidad alfa y beta, además de explorar la composición taxonómica y mapear covariables de forma sencilla. Su integración con FROGS y la exportación de resultados lo hacen una herramienta versátil. Es ideal para una rápida exploración de datos y formación, simplificando el acceso al estudio del microbioma.
   
MicrobiomeAnalyst v2.0 https://www.microbiomeanalyst.ca/MicrobiomeAnalyst/home.xhtml
   - **Descripción:** MicrobiomeAnalyst es una plataforma web fácil de usar para el análisis estadístico, visual y meta-análisis integral de datos de microbioma. Está diseñada para ser accesible a investigadores y clínicos con diversos niveles de experiencia en bioinformática, proporcionando una interfaz intuitiva para explorar una amplia variedad de métodos establecidos. MicrobiomeAnalyst soporta datos de genes marcadores (por ejemplo, 16S rRNA) y datos de metagenómica de escopeta (shotgun). Sus módulos permiten el perfilado de la comunidad, análisis comparativos, predicción funcional e integración con conjuntos de datos públicos o firmas microbianas conocidas. Las funcionalidades clave incluyen el procesamiento y la normalización de datos, diversos análisis estadísticos (por ejemplo, abundancia diferencial), visualizaciones interactivas (por ejemplo, gráficos PCoA, mapas de calor, redes de correlación) e interpretación funcional (por ejemplo, análisis de redes metabólicas). También ofrece un módulo de procesamiento de datos brutos para datos de amplicones y un módulo para integrar datos de microbioma y metabolómica.

## Metodología:

## 1.	Acceso al servidor de cómputo

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

# Repetir el mismo comando para los barcodes b02, b03, b04, b05, b06, b07, b08, b17 y b18

multiqc -o masato_its .
```

## 3. Limpieza de los archivos FASTQ 

```bash
cd ~/metataxonomic

mkdir trim

cd trim

gunzip -c /data/2025_1/sequencing/metataxonomica/its_curso/b01.fastq.gz | NanoFilt -q 10 --length 600 --maxlength 2000 | gzip > b01_its.fastq.gz

# Repetir el mismo comando para los barcodes b02, b03, b04, b05, b06, b07, b08, b17 y b18
```

```bash
# Crear el script count.sh con nano:

nano count.sh

# Pegar la siguiente información:

#!/bin/bash

echo "--- Conteo de Lecturas por Muestra (Archivos FASTQ.GZ) ---"
echo "Archivo FASTQ.GZ | Número de Lecturas"
echo "-----------------------------------"

# Loop through all .fastq.gz files in the current directory
for file in *.fastq.gz; do
    if [ -f "$file" ]; then
        # Decompress the file on the fly and count lines
        num_lines=$(zcat "$file" | wc -l | awk '{print $1}')
        # Each read in a FASTQ file (compressed or not) consists of 4 lines
        num_reads=$(echo "$num_lines / 4" | bc)
        printf "%-20s | %s\n" "$file" "$num_reads"
    fi
done

echo "-----------------------------------"

# Ejecutar el script:

sh count.sh
```

## 4. Identificación del perfil taxonómico a partir de los archivos FASTQ

```bash
cd ~/metataxonomic/

mkdir taxonomy

cd taxonomy

conda activate metataxonomic

emu abundance --type map-ont --threads 10 --db /data/db/emu/unite/ --output-dir . ~/metataxonomic/trim/b01_its.fastq.gz

# Repetir el mismo comando para los barcodes b02, b03, b04, b05, b06, b07, b08, b17 y b18

emu combine-outputs . tax_id
```
## 5. Obtención del archivo BIOM

```bash
awk 'BEGIN{ FS = OFS = "\t" } { print "", $0 }' emu-combined-tax_id.tsv > masato_its_emu.tsv

awk '{if (NR == 1) {print $0} else {print NR-2, $0}}' masato_its_emu.tsv > tmp && mv tmp masato_its_emu.tsv

tr '\t' ',' < masato_its_emu.tsv | sed 's/_nanofilt.fastq//g' > masato_its_emu.csv

awk 'BEGIN{FS=OFS=","}{sub(/\r$/,"");print $1,$9,$8,$7,$6,$5,$4,$3,$2,$10,$11,$12,$13,$14,$15,$16,$17,$18,$19}' masato_its_emu.csv > tmp && mv tmp masato_its_emu.csv

sed 's/,$//' masato_its_emu.csv | sed 's/superkingdom/Kingdom/g' | sed 's/phylum/Phylum/g' | sed 's/class/Class/g' | sed 's/order/Order/g' | sed 's/family/Family/g' | sed 's/genus/Genus/g' | sed 's/species/Species/g' | sed 's/_its.fastq//g' > masato_its_emu_final.csv

## Crear en Windows la carpeta metataxonomic, copiar el archivo CSV final y descomprimir el archivo biom.rar que está en el aula virtual.

## Abrir RStudio y seguir las indicaciones del docente.

## Cargar el archivo generado en el programa Easy16S (https://shiny.migale.inrae.fr/app/easy16S) y exportarlo en BIOM (junto con su metadata en CSV).
```

## 6. Análisis de diversidad  

Abrir el archivo BIOM en el programa microbiomeanalyst(https://www.microbiomeanalyst.ca/MicrobiomeAnalyst/ModuleView.xhtml) y seguir las indicaciones del docente

## 7. Analisis metataxonómico de las secuencias 16S

```bash
La localización de las secuencias 16S esta en /data/2025_1/sequencing/metataxonomica/16s_curso/

Distribución de los barcodes: Grupo 1: b01, b02, b03, b04, b18 | Grupo 2: b05, b06, b07, b08, b17 | Grupo 3: b09, b10, b11, b12, b19 | Grupo 4: b13, b14, b15, b16, b20

Realizar la visualización de calidad de las secuencias de sus respectivos barcodes

Realizar la limpieza de los FASTQ utilizando los siguientes criterios: un Q minimo de 10, una longitud minima de secuencia de 1000 y una longitud máxima de 2000

Calcular el número total de lecturas limpias

Realizar el análisis metataxonómico de sus respectivos barcodes utilizando la base de datos localizada en /data/db/emu/emu/

Copiar los archivos *_16s.fastq_rel-abundance.tsv a la carpeta /data/2025_1/16s_alumnos

Copiar los archivos *_16s.fastq_rel-abundance.tsv que estan en la carpeta /data/2025_1/16s_alumnos segun el siguiente esquema: Grupo 1 copiara los archivos del Grupo 2, Grupo 2 copiara los archivos del Grupo 4, el Grupo 3 copiara los archivos del Grupo 1, Grupo 4 copiara los archivos del Grupo 3

Generar el archivo emu-combined-tax_id.tsv y su respectivo archivo BIOM (reemplazar masato_its por masato_16s)

Realizar el analisis de diversidad
```

# Bitácora de la práctica:

## Resultados

```bash
•	Flujograma de todo el pipeline realizado 
•	Valores de calidad y numero de lecturas en la data cruda y después de la limpieza 
•	Análisis de abundancia en todos los niveles taxonómicos
•	Análisis de diversidad alfa (seleccionar 3 métricas) a nivel de género y familia 
•	Análisis de beta diversidad (PCoA y NMDS) a nivel de género y familia
```

## Discusión

```bash
•	Explicar que métrica de la diversidad alfa representa mejor la diversidad de las muestras
•	Explicar cual metodo de ordinación de la diversidad beta explica mejor la similaridad/disimilaridad de las muestras
```
















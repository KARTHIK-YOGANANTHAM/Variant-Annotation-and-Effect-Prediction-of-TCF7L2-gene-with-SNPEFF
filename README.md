# Variant Annotation and Effect Prediction of TSF7L2 gene with SNPEFF

## Overview

This project focuses on the Variant Annotation and Effect Prediction of the **TSF7L2** gene, a key genetic locus associated with **Type 2 Diabetes(T2D)** susceptiblity.  Using **SnpEff**, a robust genomic variant annotation tool, this study provides insights into the functional consequences of genetic variations within the TCF7L2 gene.  By leveraging a curated **.VCF** file containing SNP data and the **GRCh38** reference genome, this project aims to elucidate the biological significance of known variants, such as **rs7903146**, and their impact on coding regions, regulatory elements, and gene expression pathways.

## Table of Contents
 - [Introduction](#introduction)
 - [Objectives](#objectives)
 - [Data Collection](#datacollection)
 - [Tools Installation](#toolsinstallation)
 - [Methodology](#methodology)
 - [Result](#result)
 - [Contribution](#contribution)

## Introduction

The **TCF7L2 gene** is on of the most widely studies genetic loci in Type 2 Diabetes(T2D) research due to its significant role in **glucose homeostatis** and **insulin secretion**. Variants such as **rs7903146** have been strongly associated with increased diabetes risk, making them crucial targets for functional genomics studies.  This project employs **SnpEff**, a powerful variant annotation tool, to analyze and predict the effects of genetic variations within the TCF7L2 gene.  Through systematic annotation and impact assessment, this study highlights the potential biological consequences of these variants and their implications for diabetes-related research and therapeutics.

## Objectives

 - Identify and annotate **SNPs** within the TCF7L2 gene region
 - Predict the functional effects of variants and their potential impact on gene and protein functions
 - Assess the clinical relevance of the identified variants

## Data Collection

For this analysis, two types of data are required :
1. Descriptive TCF7L2 gene variant file in **.VCF** format
2. Reference **.fasta** file for annotation of variant gene

### .VCF file collection
1. Search for the targeted **TCF7L2** gene in [Ensembl Genome Browser.](https://www.ensembl.org/index.html?redirect=no)
2. Filter the result by choosing **Variant** option on the category table or by selecting the **Variant table** on the gene information page.
3. Filteration of variant will be based on
    - Consequence type (eg: missense, frameshift, splice-site)
    - Clinical significance
    - Population frequency (MAF>0.01 *mostly preferred)
4. I have selected the variant of **rs7903146** because of its well studied SNP and its brief interrogation with T2D.
5. Copy the variant name
6. Enter the [Ensembl Variant Effector Prediction tool (VEP)](https://www.ensembl.org/info/docs/tools/vep/index.html)for downloading the .VCF file of our selected variant
7. Enter the copied variant name on the input box and click run for the processing of out .VCF file
8. After processing, download the output file in the .VCF format

### Reference file collection

1. Enter the recent archieve data file on [Ensembl Archieve Site](https://ftp.ensembl.org/pub/) (Eg: release-111/)
2. click the **fasta/** folder on the release file and select **--> homo_sapiens/ --> dna/**
3. Upon the Dna folder, copy the link of corresponding version of your variant file release (Eg: GRCh38, GRCh37)
4. Download the reference file in the format of **.fasta.gz** by clciking on the file or through
   ```bash
   wget https://ftp.ensembl.org/pub/release-111/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna.chromosome.10.fa.gz
   ```
5. Make sure you want to download the whole genome reference file or specifically download the particular chromosome file of your variant.
6. Your .vcf variant location can be identified through command
   ```bash
   head rs7903146.vcf
   ```
7. Unzip the gz fasta file

## Tools Installation
Required tools
1. **bcftools**
2. **SnpEff**
    
### bcftools Installtion
BCFtools is a command-line toolset used for processing, analyzing, and manipulating BCF (Binary Call Format) and VCF (Variant Call Format) files, which are commonly used in genomic variant analysis. It can be installed and verified throgh the command,
```bash
sudo apt install bcftools
bcftools --version
```
### SnpEff Installation
- SnpEff (short for "SNP Effect Predictor") is a bioinformatics tool primarily used for annotating and predicting the effects of genetic variants such as SNPs (single nucleotide polymorphisms), indels (insertions and deletions), and structural variations.
- For download the snpEff [click here](https://pcingola.github.io/SnpEff/download/)
- Otherwise, copy the download link from above link and install it through unix
```bash
wget https://snpeff.blob.core.windows.net/versions/snpEff_latest_core.zip
```
- **You need java preinstalled with v1.11 or higher to run the snpEff software**
- Extract the zip file and run the snpEff by entering
```linux
java -jar snpEff.jar
```
## Methodology
1. **Pre-normalizaton** of both files by splitting multiallelic sites into seperate lines for each allele
```bash
bcftools norm -m -both -o rs7903146_prenorm.vcf -i rs7903146.vcf.gz
bcftools -f reference_chr10.fasta -o rs7903146_normalised.vcf -i rs7903146_prenorm.vcf
```

2. **Finding database** for the annotation for the normalised .vcf variant file. SnpEff itself contains databases for the reference and it can be access through downloading out specific databases.
```bash
java -jar snpEff.jar databases #for updating databases
java -jar snpEff.jar | grep -i "homo_sapiens" #for finding our related databases
java -jar snpEff.jar download -v GRCh38.99 #download the specific version for annotation
```

3. **Annotation** through various parameters and requirements of our calibration.
```bash
java -jar snpEff.jar ann -nolog -noStats -no-upstream -no-downstream -no-utr -c snpEff.config -o snpEff_result.vcf -i GRCh38 rs7903146_normalised.vcf
```
- **.nolog** = Disable log message during execution to keep the output clean
- **-noStats** = Skip generation of annotation summary statistics (a separate report typically created after annotation)
- **-no-upstream** and **-no-downstream** = Disables annotation of variants in upstream and downstream regions (promoters, regulatory regions outside coding genes).
- **-no-utr** = Disables annotation of variants in untranslated regions (UTRs), which may affect gene regulation

4. **Result summary** will be retrived through running the command without **-noStats** command.
```bash
java -jar snpEff.jar ann -nolog -no-upstream -no-downstream -no-utr -c snpEff.config -o snpEff_result.vcf -i GRCh38 rs7903146_normalised.vcf
```

## Result

- The result are stored in the formats of **snpEff_result.vcf**, **snpss_summary.html** and **snpEff_genes.txt** in the project folder.
- Examine the [html file](file:///C:/Users/Karthik/Desktop/linux/variant_project/snpEff_summary.html). From this annotated html file we can get the informations of
  - Variant statistics (Total number of variant analyzed and its mean calculations)
  - Functional annotations (**Moderate** for this rs7903146 variant which resulted as **may alter gene function(missense mutation)**)
  - Gene level detailes
  - Graphical summaries

## Result for this rs7903146 variant

![Screenshot (175)](https://github.com/user-attachments/assets/8d8632b1-b382-4091-ae2b-e228a2349f81)

1. **Variant Analysis**:
   - Focused on SNPs within the **TCF7L2 gene** using the GRCh38 reference genome.
   - Special emphasis on the **`rs7903146` variant** (C and T alleles).

2. **Functional Annotation**:
   - `rs7903146` located in a **coding region** of TCF7L2.
   - Predicted as a **missense variant** with **moderate impact** on protein function.

3. **Biological Implications**:
   - Associated with altered **insulin secretion** and **Type 2 Diabetes** susceptibility.

4. **Impact Levels**:
   - 10% of variants classified as **MODERATE**, including `rs7903146`.
   - Remaining variants were either **LOW** or **modifier impact**.

5. **Outputs**:
   - **Annotated VCF file**: Includes detailed effects for all variants.
   - **Graphical report**: Visualizes variant distribution and impact in HTML and PDF.

6. **Significance**:
   - Highlights `rs7903146` as a key diabetes-associated SNP for further research.
![Screenshot (176)](https://github.com/user-attachments/assets/5f27a294-e373-4c29-a365-f84c094d9d2b)

### SnpEff text file
The snp_genes.txt file summarizes the genes affected by the variants analyzed in this project, including TCF7L2, the primary gene of interest. It lists the number of variants per gene, categorizing them by impact levels (HIGH, MODERATE, LOW, or MODIFIER) and their genomic locations (e.g., coding regions, introns, UTRs). For this project, it highlights the rs7903146 variant within TCF7L2 as a missense variant with a MODERATE impact, providing valuable insights into its potential role in altering gene function and its association with Type 2 Diabetes susceptibility. This file acts as a concise summary, aiding in prioritizing biologically significant variants for further research.

![Screenshot (178)](https://github.com/user-attachments/assets/5b7278b9-9b58-4c09-b6ea-bd434d1f001a)

### Contribution

We welcome contributions to improve this project.  Here's how you can contribute:

1. **Report Bugs**
   - If you encounter any issues, please create a new issues in the **Issues** tab

2. **Submit Enhancements**
   - Fork the repository:
     ```bash
     git fork https://github.com/KARTHIK-YOGANANTHAM/AirwayGeneExpressionAnalysis.git
     ```
   - create a pull request with a detailed description of your changes
  
### Contact

For any questions, contact me via email: karthikyoganantham23@gmail.com

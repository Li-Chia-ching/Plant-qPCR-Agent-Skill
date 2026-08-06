# Plant qPCR Agent Skill

[![DOI](https://zenodo.org/badge/1324714426.svg)](https://doi.org/10.5281/zenodo.21817268)

## Automated qPCR Primer Design and Expression Analysis Workflow

## Overview

Plant qPCR experiments often fail because of inappropriate primer design, insufficient specificity validation, or inconsistent downstream Ct-value analysis.

This Skill provides a standardized AI-assisted workflow to automate:

* genome-based qPCR primer design
* primer quality evaluation
* polyploid gene specificity assessment
* qPCR expression analysis preparation

It is designed for research groups working with plant genome assemblies and gene families.

---

# Features

## 1. Genome-aware primer design

The workflow integrates:

* genome sequence
* GFF3 annotation
* CDS
* transcript sequences

to design primers based on actual gene structures.

---

## 2. qPCR-specific optimization

Unlike cloning PCR primers, qPCR primers prioritize:

* short amplicons
* high amplification efficiency
* reproducibility
* specificity

Default targets:

```
Amplicon:
100–150 bp preferred

Maximum:
250 bp
```

---

## 3. Single exon gene handling

Many plant genes lack introns.

This Skill automatically recognizes:

* exon number
* intron availability

and provides experimental warnings instead of failing.

Example:

```
Single exon gene detected.
DNase I treatment is recommended before reverse transcription.
```

---

## 4. Polyploid plant support

Suitable for species including:

* Medicago sativa
* wheat
* cotton
* soybean
* other polyploid crops

The workflow distinguishes:

* gene-specific primers
* homeolog-compatible primers
* family-level primers

---

# Recommended Workflow

```
Genome data
      |
      ↓
Target gene validation
      |
      ↓
Gene structure analysis
      |
      ↓
Primer3 candidate generation
      |
      ↓
Specificity validation
      |
      ↓
Primer ranking
      |
      ↓
Primer report
      |
      ↓
qPCR experiment
      |
      ↓
Ct analysis
      |
      ↓
Expression visualization
```

---

# Required Input

Users should provide:

```
1. Genome FASTA

2. GFF3 annotation

3. CDS/transcript FASTA

4. Target gene list

5. Optional:
   - protein references
   - haplotype sequences
   - previous primers
```

---

# Output

The Skill generates:

## Primer table

```
Target_qPCR_Primers_Result.csv
```

including:

* primer sequences
* Tm
* GC content
* amplicon size
* specificity class
* intron information
* ranking score

## Report

```
Target_qPCR_Primers_Report.md
```

including:

* design strategy
* recommended primers
* validation summary
* experimental notes

---

# Recommended Use Cases

Suitable for:

* gene family expression studies
* candidate gene validation
* RNA-seq validation
* stress response experiments
* developmental expression analysis

Examples:

* DELLA
* WOX
* TCP
* NAM/CUC
* MYB
* bHLH
* other plant gene families

---

# Limitations

This Skill does not:

* identify new gene families
* predict tissue expression patterns
* replace experimental validation

Primer performance should still be confirmed by:

* standard PCR
* melt curve analysis
* amplification efficiency testing

---

# Version

Version: 1.0

Designed for:

Plant molecular biology laboratories

Workflow type:

AI-assisted computational biology pipeline

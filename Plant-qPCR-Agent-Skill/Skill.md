# Plant qPCR Primer Design and Expression Analysis Skill

## 1. Skill Overview

This Skill provides an automated bioinformatics workflow for plant qPCR experiment preparation, covering:

1. Target gene validation from genome annotation
2. qPCR primer design based on genomic and transcript sequences
3. Primer specificity evaluation
4. Polyploid/homologous gene compatibility assessment
5. Standardized primer report generation
6. Optional downstream qPCR Ct-value analysis and visualization

The workflow is designed for plant species with:

* chromosome-level genome assemblies
* GFF3 genome annotations
* CDS/transcript/protein sequence databases
* gene family analysis results

The Skill does not perform gene family identification. It assumes that target genes have already been identified by upstream analyses.

---

# 2. Input Requirements

Before execution, the user should provide:

## 2.1 Genome resources

Required:

```
Genome/
├── genome.fa
├── annotation.gff3
├── cds.fa
└── transcript.fa
```

Optional:

```
├── protein.fa
├── haplotype_sequences/
└── reference_species_proteins/
```

---

## 2.2 Target gene information

Required:

One of:

* target gene ID list

Example:

```
Msa085132
Msa076748
Msa069302
```

or:

```
target_gene_list.csv
```

containing:

| Gene_ID | Transcript_ID |
| ------- | ------------- |
| Gene001 | Transcript001 |

---

## 2.3 Previous primer information (optional)

If previous cloning/qPCR primers exist:

```
Previous_Primers/
└── primer_archive.txt
```

The Skill should extract:

* old primer sequences
* target genes
* previous PCR product size
* experimental problems

Previous failed primers should not be reused automatically.

---

# 3. Experimental Objective Definition

Before primer design, determine the experimental goal:

```
A. Single gene expression quantification

B. Homeolog/paralog-specific expression

C. Gene family or subgroup expression

D. Expression validation of transcriptomic results
```

The primer design strategy must match the objective.

---

# 4. Target Gene Validation Module

## Required operations

The Agent should:

1. Read target gene list.
2. Verify gene IDs against genome annotation.
3. Extract:

* chromosome/scaffold location
* strand information
* exon coordinates
* intron coordinates
* CDS sequence
* transcript sequence
* protein sequence

4. Generate:

```
Target_gene_structure.csv
```

containing:

| Gene_ID | Transcript | Exon_number | Intron_number | Length |
| ------- | ---------- | ----------- | ------------- | ------ |

---

# 5. qPCR Primer Design Module

## 5.1 Amplicon length requirements

Primary requirement:

```
80–200 bp
```

Preferred:

```
100–150 bp
```

Maximum acceptable:

```
≤250 bp
```

Candidates exceeding 250 bp must be discarded.

---

## 5.2 Intron/exon strategy

The workflow should prioritize:

### Level A

Primer pair spanning exon junctions.

### Level B

Primer pair crossing intron regions.

Requirements:

* distinguish cDNA from genomic DNA
* preferably create ≥100 bp difference between cDNA and gDNA products

### Level C

Single exon genes.

If no intron exists:

The workflow must NOT terminate.

The report must include:

```
Single exon gene.
Genomic DNA contamination cannot be excluded by primer design.
DNase I treatment before reverse transcription is required.
```

---

# 6. Primer Biochemical Parameters

Candidate primers must satisfy:

| Parameter           | Requirement                    |
| ------------------- | ------------------------------ |
| Length              | 18–25 nt                       |
| Tm                  | 58–62°C                        |
| Optimal Tm          | ~60°C                          |
| Pair Tm difference  | ≤2°C                           |
| GC content          | 40–60%                         |
| Preferred GC        | 45–55%                         |
| Secondary structure | No strong hairpin              |
| 3' end              | No strong self-complementarity |

Primer design engine:

Recommended:

```
primer3-py
```

---

# 7. Specificity Validation Module

## 7.1 Sequence databases

Validate against:

Required:

```
transcript.fa
cds.fa
```

Recommended:

```
genome.fa
```

---

## 7.2 Specificity classification

Do not simply classify as unique/non-unique.

Use:

### Class I

Gene-specific primer.

Only target locus detected.

### Class II

Target gene family member-specific.

Possible homeolog amplification.

### Class III

Subfamily/family expression primer.

Suitable only for group expression analysis.

Output:

```
Specificity_Class
```

---

# 8. Polyploid and Haplotype Compatibility

For polyploid species:

If haplotype sequences are available:

1. Compare primer binding regions.
2. Detect SNP/InDel variation.
3. Prefer conserved regions.

Report:

```
Haplotype compatibility:
- Universal
- Hap1 specific
- Hap2 specific
- Variable region
```

---

# 9. Computational Stability Requirements

## 9.1 External tool fallback

If BLAST or external tools fail:

The Agent should automatically switch to:

* Biopython sequence matching
* local alignment
* k-mer based screening

Do not terminate workflow because of missing external software.

---

## 9.2 Memory management

For large genomes:

Use:

* batch processing
* generators
* indexed FASTA access

Avoid loading complete genome sequences into memory.

---

# 10. Primer Ranking

Each gene should output:

2–3 candidate primer pairs.

Recommended scoring:

```
Specificity                 30%
Amplicon suitability        25%
Primer thermodynamics       20%
Exon/intron design          15%
Haplotype conservation      10%
```

---

# 11. Output Files

## 11.1 Primer result table

Filename:

```
Target_qPCR_Primers_Result.csv
```

Required columns:

```
Gene_ID
Transcript_ID
Primer_pair_ID
Forward_primer
Reverse_primer
Amplicon_length
Forward_Tm
Reverse_Tm
Forward_GC
Reverse_GC
Intron_crossing
Intron_length
Specificity_Class
Haplotype_compatibility
Score
Notes
```

---

## 11.2 Design report

Filename:

```
Target_qPCR_Primers_Report.md
```

Include:

* project summary
* target gene list
* design parameters
* recommended primers
* specificity interpretation
* experimental precautions
* limitations

---

# 12. Optional Module: qPCR Ct Data Analysis

## Trigger conditions

Activate when user provides:

* Ct values
* qPCR results
* relative expression request
* expression visualization request

---

# 12.1 Input format

Required columns:

```
Sample
Treatment
Target_Gene
Reference_Gene
Ct_Target
Ct_Reference
Biological_rep
Technical_rep
```

---

# 12.2 Data cleaning

The script must safely process:

* Undetermined
* empty values
* non-numeric labels

Never directly convert raw Ct columns using:

```r
as.numeric()
```

without preprocessing.

---

# 12.3 Relative expression calculation

Implement:

## ΔCt

[
\Delta Ct=Ct_{target}-Ct_{reference}
]

## ΔΔCt

[
\Delta\Delta Ct=\Delta Ct_{sample}-\Delta Ct_{control}
]

## Relative expression

[
RQ=2^{-\Delta\Delta Ct}
]

---

# 12.4 R visualization

Generate R scripts using:

* dplyr
* ggplot2

Outputs:

* summary statistics
* mean ± SE
* publication-quality figures

Recommended output:

```
Expression_summary.csv

Expression_plot.pdf
Expression_plot.tiff
```

---

# End of Skill

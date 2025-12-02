# DNA Sequence Pattern Analysis

## Problem Statement

### Table: Samples

| Column Name  | Type    | Description |
|--------------|---------|-------------|
| sample_id    | int     | Unique identifier for each sample |
| dna_sequence | varchar | DNA sequence (contains only A, T, G, C) |
| species      | varchar | Species the sample was collected from |

### Key Points:
- `sample_id` is the primary key
- Each row represents a DNA sample with its sequence and source species
- Need to identify specific patterns in the DNA sequences

## Task

For each DNA sample, identify the following patterns:
1. `has_start`: Sequence starts with "ATG" (start codon)
2. `has_stop`: Sequence ends with "TAA", "TAG", or "TGA" (stop codons)
3. `has_atat`: Sequence contains "ATAT" (repeated pattern)
4. `has_ggg`: Sequence contains at least 3 consecutive 'G's

Return a result with all original columns plus four additional boolean columns (1 for true, 0 for false) indicating which patterns are present.

## Example

### Input

**Samples table:**
| sample_id | dna_sequence     | species   |
|-----------|------------------|-----------|
| 1         | ATGCTAGCTAGCTAA  | Human     |
| 2         | GGGTCAATCATC     | Human     |
| 3         | ATATATCGTAGCTA   | Human     |
| 4         | ATGGGGTCATCATAA  | Mouse     |
| 5         | TCAGTCAGTCAG     | Mouse     |
| 6         | ATATCGCGCTAG     | Zebrafish |
| 7         | CGTATGCGTCGTA    | Zebrafish |

### Output
| sample_id | dna_sequence     | species   | has_start | has_stop | has_atat | has_ggg |
|-----------|------------------|-----------|-----------|----------|----------|---------|
| 1         | ATGCTAGCTAGCTAA  | Human     | 1         | 1        | 0        | 0       |
| 2         | GGGTCAATCATC     | Human     | 0         | 0        | 0        | 1       |
| 3         | ATATATCGTAGCTA   | Human     | 0         | 0        | 1        | 0       |
| 4         | ATGGGGTCATCATAA  | Mouse     | 1         | 1        | 0        | 1       |
| 5         | TCAGTCAGTCAG     | Mouse     | 0         | 0        | 0        | 0       |
| 6         | ATATCGCGCTAG     | Zebrafish | 0         | 1        | 1        | 0       |
| 7         | CGTATGCGTCGTA    | Zebrafish | 0         | 0        | 0        | 0       |

## Solution

```sql
SELECT 
    sample_id,
    dna_sequence,
    species,
    CASE WHEN dna_sequence LIKE 'ATG%' THEN 1 ELSE 0 END AS has_start,
    CASE 
        WHEN dna_sequence LIKE '%TAA' 
          OR dna_sequence LIKE '%TAG' 
          OR dna_sequence LIKE '%TGA' 
        THEN 1 
        ELSE 0 
    END AS has_stop,
    CASE WHEN dna_sequence LIKE '%ATAT%' THEN 1 ELSE 0 END AS has_atat,
    CASE WHEN dna_sequence LIKE '%GGG%' THEN 1 ELSE 0 END AS has_ggg
FROM 
    Samples
ORDER BY 
    sample_id;

```



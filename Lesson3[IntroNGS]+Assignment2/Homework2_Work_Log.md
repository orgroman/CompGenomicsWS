# Homework 2 Work Log

This file summarizes the conversation, decisions, commands, and results used to solve Assignment 2.

## Beginner Orientation

This assignment can look biological, but most of the work is actually computational:

1. Read a metadata table and identify which files belong to which sample.
2. Inspect compressed text files in known genomics formats.
3. Count records and string lengths.
4. Run a quality-control tool and interpret whether the data passes.
5. Inspect a reference genome file and count named sequences.

You do **not** need to be a biologist to follow the solution. The main ideas are:

- A **sample** is one biological source that was sequenced, such as a tumor biopsy or a nearby normal tissue sample.
- A **read** is one short DNA string produced by the sequencing machine.
- A **FASTQ** file stores reads. Each read uses exactly four lines: header, DNA sequence, separator, quality string.
- **Paired-end** sequencing means each DNA fragment is read from two sides, producing an `R1` file and an `R2` file.
- **FastQC** is a sanity-check tool for raw sequencing files.
- A **FASTA** file stores reference DNA sequences. The human reference `hg38` is used as a coordinate system.

The most learner-friendly deliverable is:

```text
Lesson3[IntroNGS]+Assignment2/Homework2_Step_By_Step.ipynb
```

Open that notebook when you want to understand the homework step by step. Use this work log when you want to reproduce commands or check the final numbers.

## Request

The original request was:

> make a dedicated branch for it, solve homework 2, you can use the conda env I got over my wsl Ubuntu for that

After the solution was prepared, the follow-up requests were:

> Now guide me step by step I'd like you to teach me

and:

> Ok, create a jupyter notebook that solve it step by step with explanation

This log records what was done so the work is reproducible.

## Repository State and Branch

The repository was inspected from:

```text
C:\Users\orgrd\workspace\repos\CompGenomicsWS
```

Initial repository contents:

```text
README.md
IntroEmail+Assignment1/
Lesson3[IntroNGS]+Assignment2/
```

The dedicated branch created for the work:

```bash
git checkout -b homework-2-solution
```

Current branch after the work:

```text
homework-2-solution
```

## Assignment Files Found

Assignment 2 lives in:

```text
Lesson3[IntroNGS]+Assignment2/
```

Important assignment files:

```text
README.md
Metadata_18.csv
Lecture3_2026.pdf
```

The assignment `README.md` asks about:

- Metadata terms: `neoplasm`, `normal tissue adjacent to neoplasm`
- Sequencing strategy: `WXS`
- FASTQ headers, read length, and read counts
- FastQC quality and base composition reports
- The `hg38` human reference genome
- Number of reference sequences and chromosome lengths

## WSL and Conda Setup

WSL distributions were inspected with:

```powershell
wsl.exe -l -v
```

Ubuntu was available, but the default WSL distro was not a normal Ubuntu shell. Commands were then targeted explicitly at Ubuntu:

```powershell
wsl.exe -d Ubuntu -e /bin/bash -lc "..."
```

Conda was not on the default PATH, so it was located manually:

```bash
/home/orgr/anaconda3/bin/conda
```

Conda environments found:

```text
base
bioinfo
```

The `bioinfo` environment already contained `samtools`, but did not initially contain `fastqc`.

FastQC was installed into the `bioinfo` environment:

```bash
~/anaconda3/bin/conda install -n bioinfo -y -c conda-forge -c bioconda fastqc
```

Installed FastQC version:

```text
FastQC v0.12.1
```

## Data Directory

Large downloaded data were kept outside the git repo in WSL:

```bash
~/compgenomicsws_hw2
```

Created with:

```bash
mkdir -p ~/compgenomicsws_hw2
```

## Data Download

The assignment instructed downloading four paired-end FASTQ files and the `hg38` reference.

FASTQ files were downloaded from ENA/SRA FTP:

```bash
cd ~/compgenomicsws_hw2

curl -L --retry 3 -O ftp://ftp.sra.ebi.ac.uk/vol1/fastq/ERR483/007/ERR4833597/ERR4833597_1.fastq.gz
curl -L --retry 3 -O ftp://ftp.sra.ebi.ac.uk/vol1/fastq/ERR483/007/ERR4833597/ERR4833597_2.fastq.gz
curl -L --retry 3 -O ftp://ftp.sra.ebi.ac.uk/vol1/fastq/ERR483/001/ERR4833621/ERR4833621_1.fastq.gz
curl -L --retry 3 -O ftp://ftp.sra.ebi.ac.uk/vol1/fastq/ERR483/001/ERR4833621/ERR4833621_2.fastq.gz
```

The `hg38` reference was downloaded from UCSC:

```bash
curl -L --retry 3 -O http://hgdownload.cse.ucsc.edu/goldenPath/hg38/bigZips/hg38.fa.gz
```

The reference download initially interrupted, then was resumed with:

```bash
curl -L -C - --retry 3 -O http://hgdownload.cse.ucsc.edu/goldenPath/hg38/bigZips/hg38.fa.gz
```

Downloaded files:

```text
ERR4833597_1.fastq.gz
ERR4833597_2.fastq.gz
ERR4833621_1.fastq.gz
ERR4833621_2.fastq.gz
hg38.fa.gz
```

## Metadata Observations

`Metadata_18.csv` contains duplicate column names, so PowerShell `Import-Csv` could not parse it cleanly. The file was inspected as raw CSV text instead.

Important metadata rows:

```text
Sample 8 / ERR4833597 / AD0699_18N / normal tissue adjacent to neoplasm / germline genotype / WXS
Sample 32 / ERR4833621 / AD0729_18T / neoplasm / somatic genotype / WXS
```

Interpretation:

- `ERR4833597` is the matched normal sample.
- `ERR4833621` is the tumor sample.
- `WXS` means whole-exome sequencing.
- The library layout is paired-end.

## FASTQ Inspection

The first normal R1 record was inspected with:

```bash
zcat ERR4833597_1.fastq.gz | head -4
```

Output:

```text
@ERR4833597.1 HISEQ:451:C78DHACXX:7:1101:10000:14561/1
GGCGGCGGCGGCGGCGGCGGCGGCGGCGGCCGGGGCTGGTGGGGGAGGGGGGGTGGCCCCTGATCCTCCGGTAGCGAAGGGCTAGGGGCACACGCCCAGC
+
BBBBFBF<FF7B########################################################################################
```

The first normal R2 header was:

```text
@ERR4833597.1 HISEQ:451:C78DHACXX:7:1101:10000:14561/2
```

The first tumor paired headers were:

```text
@ERR4833621.1 HISEQ:451:C78DHACXX:1:1101:10000:16167/1
@ERR4833621.1 HISEQ:451:C78DHACXX:1:1101:10000:16167/2
```

Conclusion:

- R1 and R2 share the same read identifier.
- The mate suffix differs: `/1` for R1 and `/2` for R2.

## Read Length and Read Counts

Read length was checked with:

```bash
zcat ERR4833597_1.fastq.gz | awk 'NR==2{print length($0); exit}'
```

Result:

```text
100
```

Read counts were computed by counting lines and dividing by 4:

```bash
expr $(zcat ERR4833597_1.fastq.gz | wc -l) / 4
expr $(zcat ERR4833597_2.fastq.gz | wc -l) / 4
expr $(zcat ERR4833621_1.fastq.gz | wc -l) / 4
expr $(zcat ERR4833621_2.fastq.gz | wc -l) / 4
```

Results:

```text
ERR4833597_1.fastq.gz: 28,927,775 reads
ERR4833597_2.fastq.gz: 28,927,775 reads
ERR4833621_1.fastq.gz: 36,383,853 reads
ERR4833621_2.fastq.gz: 36,383,853 reads
```

All reads are `100 bp`.

## FastQC

FastQC was run on all four FASTQ files:

```bash
cd ~/compgenomicsws_hw2
mkdir -p fastqc
~/anaconda3/bin/conda run -n bioinfo fastqc -t 4 -o fastqc \
  ERR4833597_1.fastq.gz \
  ERR4833597_2.fastq.gz \
  ERR4833621_1.fastq.gz \
  ERR4833621_2.fastq.gz
```

FastQC outputs:

```text
ERR4833597_1_fastqc.html
ERR4833597_1_fastqc.zip
ERR4833597_2_fastqc.html
ERR4833597_2_fastqc.zip
ERR4833621_1_fastqc.html
ERR4833621_1_fastqc.zip
ERR4833621_2_fastqc.html
ERR4833621_2_fastqc.zip
```

Relevant FastQC summary:

```text
ERR4833597_1: PASS Basic Statistics, PASS Per base sequence quality, PASS Per base sequence content
ERR4833597_2: PASS Basic Statistics, PASS Per base sequence quality, PASS Per base sequence content
ERR4833621_1: PASS Basic Statistics, PASS Per base sequence quality, PASS Per base sequence content
ERR4833621_2: PASS Basic Statistics, PASS Per base sequence quality, PASS Per base sequence content
```

Other notes:

```text
Tumor files had WARN for Per tile sequence quality.
All files had WARN for Per sequence GC content.
```

Quality interpretation:

- Overall base qualities are good.
- All four files pass `Per base sequence quality`.
- Quality declines toward the 3' end, especially in R2 files.
- There is a visible R2 dip around cycles `50-53`.
- There is a visible R1 dip around cycles `76-77`.

Base-content interpretation:

- All four files pass `Per base sequence content`.
- The first roughly `10-15` bases show composition bias.
- After early cycles, base composition stabilizes.
- This is common in real sequencing libraries and may reflect priming or capture/library-preparation effects.

## hg38 Reference Analysis

Number of sequences was counted with:

```bash
zgrep -c '^>' hg38.fa.gz
```

Result:

```text
455
```

The first sequence names included:

```text
chr1
chr10
chr11
chr11_KI270721v1_random
chr12
chr13
chr14
chr14_GL000009v2_random
chr14_GL000225v1_random
chr14_KI270722v1_random
```

The last sequence names included:

```text
chrUn_KI270755v1
chrUn_KI270756v1
chrUn_KI270757v1
chrUn_GL000214v1
chrUn_KI270742v1
chrUn_GL000216v2
chrUn_GL000218v1
chrX
chrY
chrY_KI270740v1_random
```

Interpretation:

- `chr1` through `chr22`, `chrX`, and `chrY` are the main chromosomes.
- `_random` sequences are unlocalized or random scaffolds associated with a chromosome.
- `chrUn_...` sequences are unplaced contigs.
- `KI...` and `GL...` names are accession-like names for alternate or auxiliary contigs.

Chromosome lengths were computed by streaming the gzipped FASTA and summing bases per sequence.

Results:

```text
chr1:  248,956,422 nucleotides
chr20: 64,444,167 nucleotides
```

## Files Created in the Repository

Two deliverable files were created:

```text
Lesson3[IntroNGS]+Assignment2/Homework2_Solution.md
Lesson3[IntroNGS]+Assignment2/Homework2_Step_By_Step.ipynb
```

This work log was then added:

```text
Lesson3[IntroNGS]+Assignment2/Homework2_Work_Log.md
```

## Teaching Flow We Discussed

The step-by-step explanation was:

1. Understand the assignment folder and files.
2. Identify the matched normal and tumor samples from metadata.
3. Learn the FASTQ four-line structure.
4. Compare paired-end R1/R2 headers.
5. Compute read length from the sequence line.
6. Compute read counts by dividing FASTQ line counts by four.
7. Run FastQC and inspect quality/base-composition modules.
8. Understand `hg38` as the UCSC name for `GRCh38`.
9. Count FASTA sequences by counting header lines beginning with `>`.
10. Interpret chromosome, random, unplaced, and auxiliary contig names.
11. Compute chromosome lengths from the FASTA.

## Final Answers

Q1:

`neoplasm` means tumor tissue. `normal tissue adjacent to neoplasm` means nearby non-tumor matched normal tissue.

Q2:

`WXS`/`WES` means whole-exome sequencing. Whole-genome sequencing would produce much larger FASTQ files, usually tens of GB per compressed FASTQ file.

Q3:

R1 and R2 headers match except for `/1` versus `/2`.

Q4:

Reads are `100 bp`. Normal files contain `28,927,775` reads each. Tumor files contain `36,383,853` reads each.

Q5:

Overall base qualities are good and pass FastQC, with expected 3' decline and some cycle-specific dips.

Q6:

Per-base sequence content passes, with early-cycle composition bias that stabilizes after the first bases.

Q7:

`hg38` is UCSC's name for the human `GRCh38` reference genome assembly.

Q8:

The downloaded reference contains `455` sequences.

Q9:

Sequence names identify main chromosomes plus random, unplaced, alternate, and auxiliary contigs.

Q10:

`chr1` has `248,956,422` nucleotides. `chr20` has `64,444,167` nucleotides.

# Homework 2 Solution (Verbatim Questions)

This file copies each homework question exactly from `README.md` and places the relevant answer directly below it.

## **Q1:** The file [Metadata_18.csv](Metadata_18.csv) contains additional meta information about the files we are downloading. What does 'neoplasm' and 'normal tissue adjacent to neoplasm' mean? </br>

`Neoplasm` means the tumor sample.

`Normal tissue adjacent to neoplasm` means tissue taken near the tumor, from the same patient, that is not tumor tissue. It is the normal/control sample used for comparison.

## **Q2:** The 23rd column (LIBRARY_STRATEGY) is marked as WXS. What is Whole Exome Sequencing (WXS/WES)? What would be the fastq file sizes if we did Whole Genome Sequencing (WGS) instead?</br></br>

`WXS`/`WES` means whole-exome sequencing. It looks only at the protein-coding parts of genes, not the whole genome.

If this were whole-genome sequencing (`WGS`), the FASTQ files would be much larger. They would usually be tens of GB per file, often around `30-100+ GB` per compressed FASTQ file.

## **Q3:** What is the difference between the header of the first read in R1 and the header of the first read in R2?</br>

For the normal sample:

- `R1`: `@ERR4833597.1 HISEQ:451:C78DHACXX:7:1101:10000:14561/1`
- `R2`: `@ERR4833597.1 HISEQ:451:C78DHACXX:7:1101:10000:14561/2`

The read identifier is the same in both. The only difference is the last part:

- `/1` means read 1
- `/2` means read 2

## **Q4:** What is the read length? How many reads are there per file?</br></br>

The read length is `100 bp` in all four FASTQ files.

Read counts:

- `ERR4833597_1.fastq.gz`: `28,927,775`
- `ERR4833597_2.fastq.gz`: `28,927,775`
- `ERR4833621_1.fastq.gz`: `36,383,853`
- `ERR4833621_2.fastq.gz`: `36,383,853`

## **Q5:** Check the "Per base sequence quality". Do the overall base qualities look ok? Do you notice any strange behavior?</br>

Overall base qualities look good. All four files pass FastQC's `Per base sequence quality` check.

Notable behavior:

- Quality gradually drops toward the 3' end, especially in `R2`.
- There is a visible dip around cycles `50-53` in `R2`.
- There is a visible dip around cycles `76-77` in `R1`.

These patterns are visible, but the data still look good overall.

## **Q6:** Does the "Per base sequence content" behave as expected?</br>

Yes, mostly. All four files pass `Per base sequence content`.

The first roughly `10-15` bases show some bias, and after that the base proportions become more stable. This is common in real sequencing data and does not mean the files failed.

## **Q7:** What is hg38?</br>

`hg38` is the UCSC name for the human reference genome `GRCh38`.

## **Q8:** How many sequences are present in the human reference genome you just downloaded?</br>

The downloaded `hg38.fa.gz` file contains `455` sequences.

## **Q9:** Just by looking at the sequence names can you understand what sequences they are?</br>

Yes, in a general way:

- `chr1`-`chr22`, `chrX`, and `chrY` are the main chromosomes.
- Names with `_random` are extra sequences linked to a chromosome.
- Names starting with `chrUn_` are sequences not placed on a specific chromosome.
- Names containing `KI...` or `GL...` are extra reference sequences.

## **Q10:** How many nucleotides are there in chromosome 1? How many in chromosome 20?

- `chr1`: `248,956,422` nucleotides
- `chr20`: `64,444,167` nucleotides

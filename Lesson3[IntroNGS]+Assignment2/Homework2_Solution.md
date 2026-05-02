# Homework 2 Solution

This solution was prepared from `Metadata_18.csv`, the downloaded FASTQ files, FastQC reports, and the downloaded `hg38.fa.gz` reference.

## Q1. What do "neoplasm" and "normal tissue adjacent to neoplasm" mean?

`Neoplasm` means an abnormal mass of tissue, here the tumor biopsy.

`Normal tissue adjacent to neoplasm` means non-tumor tissue sampled near the tumor site. It is used as the matched normal sample from the same patient, so tumor-specific somatic variants can be distinguished from germline variants.

## Q2. What is WXS/WES? What would the FASTQ file sizes be if this were WGS instead?

`WXS` means whole-exome sequencing (`WES`), where sequencing is focused on protein-coding exons rather than the whole genome.

If this were whole-genome sequencing (`WGS`), the FASTQ files would be much larger because the full human genome is sequenced. Instead of a few GB per compressed FASTQ file as in this WES dataset, typical human WGS FASTQ files are usually in the tens of GB per file, often roughly `30-100+ GB` per file depending on read length, depth, compression, and tumor/normal coverage.

## Q3. What is the difference between the header of the first read in R1 and the header of the first read in R2?

For the normal sample:

`R1`: `@ERR4833597.1 HISEQ:451:C78DHACXX:7:1101:10000:14561/1`

`R2`: `@ERR4833597.1 HISEQ:451:C78DHACXX:7:1101:10000:14561/2`

The read identifier is the same for the paired read. The only difference is the mate suffix:

- `/1` marks read 1
- `/2` marks read 2

## Q4. What is the read length? How many reads are there per file?

The read length is `100 bp` in all four FASTQ files.

Read counts:

- `ERR4833597_1.fastq.gz`: `28,927,775`
- `ERR4833597_2.fastq.gz`: `28,927,775`
- `ERR4833621_1.fastq.gz`: `36,383,853`
- `ERR4833621_2.fastq.gz`: `36,383,853`

These counts match the FastQC reports.

## Q5. Do the overall base qualities look OK? Do you notice anything strange?

Overall quality looks good. All four files passed FastQC's `Per base sequence quality` module, and most positions have high Phred scores.

Notable quirks:

- The first few cycles are slightly lower quality than the middle of the reads.
- Quality gradually drops toward the 3' end, especially in the `R2` files and in the tumor sample.
- There is a visible dip around cycles `50-53` in the `R2` files.
- There is a sharper dip around cycles `76-77` in the `R1` files.

Even with these patterns, the data are generally high quality.

## Q6. Does the "Per base sequence content" behave as expected?

It passes in all four files, but it is not perfectly flat at the beginning of the reads.

The first roughly `10-15` bases show composition bias. After that, base fractions stabilize. This early-cycle deviation is common in real sequencing data and can come from library-preparation effects such as random priming or capture-related bias.

## Q7. What is hg38?

`hg38` is the UCSC name for the human reference genome assembly `GRCh38`, the 38th major build of the human genome reference.

## Q8. How many sequences are present in the downloaded human reference genome?

The downloaded `hg38.fa.gz` file contains `455` sequences.

## Q9. Just by looking at the sequence names can you understand what sequences they are?

Yes, broadly:

- `chr1` to `chr22`, `chrX`, and `chrY` are the main chromosomes.
- Names ending with `_random` are unlocalized or random scaffolds associated with a chromosome.
- Names beginning with `chrUn_` are unplaced contigs not assigned to a specific chromosome position.
- Accession-like suffixes such as `KI...` or `GL...` identify alternate scaffolds or auxiliary contigs.

## Q10. How many nucleotides are there in chromosome 1 and chromosome 20?

- `chr1`: `248,956,422` nucleotides
- `chr20`: `64,444,167` nucleotides

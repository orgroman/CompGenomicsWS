# Homework 2 Final Answers

## Q1: The file [Metadata_18.csv](Metadata_18.csv) contains additional meta information about the files we are downloading. What does 'neoplasm' and 'normal tissue adjacent to neoplasm' mean? </br>

`Neoplasm` means the tumor sample.

`Normal tissue adjacent to neoplasm` means nearby tissue from the same patient that is not the tumor. It is used as the comparison sample.

In this homework, `ERR4833621` / `AD0729_18T` is the tumor sample, and `ERR4833597` / `AD0699_18N` is the normal comparison sample.

## Q2: The 23rd column (LIBRARY_STRATEGY) is marked as WXS. What is Whole Exome Sequencing (WXS/WES)? What would be the fastq file sizes if we did Whole Genome Sequencing (WGS) instead?</br></br>

`WXS`/`WES` means whole-exome sequencing. It looks only at the protein-coding parts of genes, not all human DNA.

If this were whole-genome sequencing (`WGS`), the FASTQ files would be much larger, usually tens of GB per file.

In the metadata for this homework, all four downloaded files are marked `WXS`.

## Q3: What is the difference between the header of the first read in R1 and the header of the first read in R2?</br>

For the normal sample, the first two headers are:

`@ERR4833597.1 HISEQ:451:C78DHACXX:7:1101:10000:14561/1`

`@ERR4833597.1 HISEQ:451:C78DHACXX:7:1101:10000:14561/2`

The headers are the same except for the last part: `R1` ends with `/1` and `R2` ends with `/2`.

That means they are the two paired reads from the same DNA fragment.

## Q4: What is the read length? How many reads are there per file?</br></br>

The read length is `100 bp` in all four files.

For example, the first sequence in `ERR4833597_1.fastq.gz` has 100 DNA letters.

Read counts:

`ERR4833597_1.fastq.gz`: `28,927,775`

`ERR4833597_2.fastq.gz`: `28,927,775`

`ERR4833621_1.fastq.gz`: `36,383,853`

`ERR4833621_2.fastq.gz`: `36,383,853`

## Q5: Check the "Per base sequence quality". Do the overall base qualities look ok? Do you notice any strange behavior?</br>

Yes. Overall base quality looks good.

FastQC reports `PASS` for `Per base sequence quality` in all four files, so the quality seems acceptable overall.

At the same time, the quality plot is not perfectly flat. Quality drops a bit near the end of the reads, this is stronger in `R2`, and there are a few dips at specific positions.

For example, the plots show visible dips around cycles `50-53` in `R2` and around cycles `76-77` in `R1`.

So the reads look good overall, but there is still some uneven behavior in the quality across the read.

## Q6: Does the "Per base sequence content" behave as expected?</br>

Yes, mostly.

The `Per base sequence content` plot is not perfectly even at the beginning of the reads. In the first `10-15` bases, the `A/C/G/T` percentages are more uneven, and later in the read they become more stable.

Since FastQC still reports this module as `PASS`, the pattern seems acceptable overall, even though the start of the reads is not perfectly balanced.

## Q7: What is hg38?</br>

`hg38` is the name of the human reference genome used here.

Quick Google search:
> hg38 (GRCh38) is the Genome Reference Consortium Human Build 38, released in December 2013 as a major update to the human reference genome. It fixes thousands of errors, includes centromere models, and updates coordinates compared to hg19, featuring improved representation of structural variations and alternative loci."

## Q8: How many sequences are present in the human reference genome you just downloaded?</br>

The downloaded `hg38.fa.gz` file contains `455` sequences.

That number comes from counting the sequence entries in that file.

## Q9: Just by looking at the sequence names can you understand what sequences they are?</br>

Yes, in a general way. `chr1`-`chr22`, `chrX`, and `chrY` are the main chromosomes. The other names are extra pieces that are also included in the reference file.

Examples from this file are `chr1`, `chr20`, `chrX`, `chrUn_KI270756v1`, and `chrY_KI270740v1_random`.

## Q10: How many nucleotides are there in chromosome 1? How many in chromosome 20?

`chr1`: `248,956,422` nucleotides

`chr20`: `64,444,167` nucleotides

So `chr1` is much larger than `chr20`.

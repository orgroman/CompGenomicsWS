# Group D Final Project GitHub Pages Site

This folder is the curated GitHub Pages version of the final project report.

## Publishing

In GitHub, enable Pages with:

- Source: `Deploy from a branch`
- Branch: `main`
- Folder: `/docs`

## What is included

The site includes only small, presentation-ready outputs:

- Summary figures in `assets/`
- Small TSV result tables in `data/`
- Provided QC/run HTML reports in `reports/`
- Executed notebooks and rendered notebook HTML in `notebooks/`
- A presenter-ready slide deck in `presentation.html`
- A printable presentation PDF in `GroupD_FinalProject_Presentation.pdf`

## What is intentionally excluded

Large or sensitive analysis inputs are not published here:

- NCBI API key files
- raw FASTQ files
- host reference genomes
- BAM/BAI files
- Kraken2 databases and bulk classifier output
- Flye intermediate files
- downloaded binaries and archives

The notebooks describe how those files can be regenerated locally when the private/raw data and public reference downloads are available.

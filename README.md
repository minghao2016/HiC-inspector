HiC-inspector
========

## About

High-throughput conformation capture (Hi-C) allows to study the three-
dimensional architecture of whole genomes through the detection of long range
chromosomal interactions. We developed HiC-inspector, a bioinformatics
pipeline to facilitate the analysis of HiC dataset. Our tools performs read
alignment, filtering of the reads that are in the DNA fragment size window
around the restriction enzyme sites, counting of interactions with a user-
defined resolution, and generation of contact matrix and heatmaps with a
complete mapping of the interaction in the reference genome. HiC-inspector is
publicly available open source, and can be used for paired-end sequencing from
different platforms.

## Authors

HiC-inspector is written by Giancarlo Castellano, François Le Dily, Antoni
Hermoso Pulido and Guglielmo Roma from Bioinformatics Core and Miguel Beato's
Lab @ CRG.

## License

This software is distributed under the terms of GPL 3.0

## Source

You can grab the last code from:

[https://github.com/HiC-inspector/HiC-inspector](https://github.com/HiC-
inspector/HiC-inspector)

## Citation

Castellano, G., Le Dily, F., Hermoso Pulido, A., Beato, M., & Roma, G. (2015). *Hi-Cpipe: a pipeline for high-throughput chromosome capture*.	 bioRxiv. Cold Spring Harbor Labs Journals. doi:[10.1101/020636](http://dx.doi.org/10.1101/020636 )

## Contact

If you have any comments, suggestions, questions, bug reports, etc., you can
submit an issue in Github or feel free to contact: Guglielmo.Roma@gmail.com,
Giancarlo.Castellano@gmail.com or toni.hermoso@crg.eu PLEASE attach your
command line and log messages if possible.

## README

### Introduction

With the improvement of sequencing techniques, chromatin digestion with a
restriction enzyme, ligation of the resulting sticky ends followed by high
throughput sequencing is getting popular to study genome-wide DNA-DNA
interactions. We present a novel pipeline, named HiC-inspector, for
identifying DNA interacting sites.

### Requirements

  * awk, zcat, GNUTools (they are commonly in most Linux distributions)
  * perl. Required modules are normally present in most Linux distributions. Alternately, used modules are: ''Getopt::Long'', ''Data::Dumper'', ''Pod::Usage'', ''POSIX 'strftime''', ''Benchmark'' and ''Parallel::ForkManager''.
  * R http://www.r-project.org (tested in 2.13 and 3.2) Install also the following R libraries:
    * gplots http://cran.r-project.org/web/packages/gplots/index.html
    * RColorBrewer http://cran.r-project.org/web/packages/RColorBrewer/index.html
    * MCMCpack http://mcmcpack.wustl.edu/installation.html 
    * Heatplus http://www.bioconductor.org/packages/release/bioc/html/Heatplus.html
    * HiTC http://www.bioconductor.org/packages/release/bioc/html/HiTC.html
  * Bowtie http://bowtie-bio.sourceforge.net (tested in 0.12.7)
  * Bedtools http://code.google.com/p/bedtools (tested in 2.10.1)

### Install

Please check the file 'INSTALL' in the distribution.

#### conf.pl

  * In this file you can change some of the path and parameters used by the application.
  * Don't forget the trailing slash at the end the dir's path.

<pre>
BEGIN {
    package main;

    %conf = (
        # Email address
                'email'                 => 'myemail@example.com',
        # Path to local tools
                'rdir'                  => "/soft/general/R-2.13/bin/",
                'bowtiedir'     => "/data/projects/hic/bin/",
                'bedtoolsdir'   => "/data/projects/hic/bin/",
                # Debug while running
                'debug'                 =>1
        );
}

1;
</pre>

### Usage

<pre style="white-space: pre-wrap; text-align:justify;">
Usage: perl
hic-inspector.pl [-n missmatches] [-m multiplemappings] [-cf chrsizefile] [-df
designfile] [-sf selectfile] [-dd datadir] [-rd restrictiondir] [-dfo
dataformat] [-pd projectdir] [-g genome] [-fs fragmentsize] [-b bin] [-s step]
[-t test] [-u utils] [-pr processors] [-h help]

Options:

-n, -missmatches=<int> Bowtie option -n: Max mismatches in seed (can be 0-3, default: -n 2) 
-m, -multiplemappings=<int> Bowtie option -m: Suppress all alignments if > <int> exist (def: no limit) 
-pd, -projectdir=<dir> Directory where to write all the results. This folder is created by the pipeline, if it does not exist 
-df, -designfile=<file> Input file describing the experimental design (tab separated text: 1st-column is sample_name, 2nd-column is read1_file, 3rd-column is read2_file, 4th-column is restriction_enzyme_file) 
-dd, -datadir=<dir> Directory containing data to be analysed. These can be raw reads in qseq or fastq format(compressed or not), or mapped reads in BED format 
-rd, -restrictiondir=<dir> Directory containing restriction enzyme sites to be considered in the analysis. Should be provided in BED format 
-dfo, -dataformat=<dir> Format of sequencing reads. Valid options are: qseq (default), fastq, and bed 
-g, -genome=<file> Indexed genome file for the reads alignment 
-sf, -selectfile=<file> Input file with user-defined genomic regions of interest (BED format) 
-fs, -fragment_size=<num> Maximum expected fragment size 
-cf, -chrsizefile=<file> Input file with chromosome sizes (tab separated text: 1st-column is chr, 2nd-column is size) 
-b, -bin=<list> Genomic windows, or "bins", to count for chromatin interactions. Several bins can be provided as a comma separated string (e.g. -b 100000,1000000) 
-s, -step=<list> Analysis steps to be performed. More steps can be provided either as comma-separated list [e.g. 1,2,3] or as dash-separated range [e.g. 1-3] 
Available steps: 
	STEP 1: copying files to local directory; 
	STEP 2: converting qseq file to FASTQ format; 
	STEP 3: mapping reads to genome; 
	STEP 4: converting mapping output to BED format; 
	STEP 5: filtering reads by proximity to restriction sites; 
	STEP 6: filtering reads for regions of interest; 
	STEP 7: combining mate filtered outputs; 
	STEP 8: calculating distances distribution between mate pairs; 
	STEP 9: generating contact matrix; STEP 10: analyzing contact matrix; 
-t, -test Test mode. Prints out commands without executing them 
-u, -utils Specify the genome release to be used among those already provided by HiC-inspector (e.g. hg19) 
-pr,   -processors=<int>               Number of processors to be used -for allowing parallelization (default: 2)
-help This documentation. 
</pre>

## Example result

We provide an example result at: [http://biocore.crg.cat/software/HiC-
inspector/](http://biocore.crg.cat/software/HiC-inspector/)

This uses [hg19](http://hgdownload.cse.ucsc.edu/downloads.html) processed with
[hindIII](http://en.wikipedia.org/wiki/HindIII) and a couple of reads from [SRR027956](http://trace.ncbi.nlm.nih.gov/Traces/sra/sra.cgi?cmd=viewer&m=data&s=viewer&run=SRR027956).

* For convenience, we provide hg19 and hindIII processed files  [here](http://biocore.crg.cat/software/HiC-inspector/extra/hg19.tar.bz2) (md5sum: e3509b155ded6e3b5eb98e996752ba05).

### Design file

Named <code>design.GM.hindIII.hg19</code>
<pre>GM.hindIII SRR027956.lite.sra_1.fastq SRR027956.lite.sra_2.fastq hindIII.hg19.bed</pre>

### Chromosome files

In utils directory you need to put a:
* chromosome sizes file
* a directory with pre-built Bowtie genome indexes

We used [fetchChromSizes](http://hgdownload.cse.ucsc.edu/admin/exe/linux.x86_64/fetchChromSizes) script to create the chrom.sizes file for the UCSC database you are working with (e.g. hg19)


### Executed command

<pre>perl mypath/hic-inspector.pl -df design.GM.hindIII.hg19 -dd inputreadsdir -pd output/myproject.hindIII.hg19 -dfo fastq -u hg19 -b 1000000,10000000</pre>


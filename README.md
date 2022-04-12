# covid-truth-datasets

SARS-CoV-2 datasets for testing assembly/consensus-calling pipelines.

The VCF fies in this repository are in the correct format to be used
with [covid-truth-eval](https://github.com/iqbal-lab-org/covid-truth-eval).

Each sample from each data set has:
* at least one Illumina and one Nanopore run
* a "truth VCF" file of the expected variants, using the
  standard reference genome MN908947.3.


## Datasets

All sample names and accessions are listed in the file
`accessions.tsv`. Although each set is named using the ENA project accession
(`PRJ*`), that does not mean that the whole project was used for the
dataset.

### PRJEB51850

This comprises 16 samples, all of which where sequenced using the ARTIC
protocol version 3. Each sample has an Illumina and Nanopore run.


### PRJEB50520

This is 12 samples across a range of lineages. Each sample has sequencing
runs using:
* Illumina ARTIC version 4.1
* Nanopore ARTIC version 4.1
* Nanopore Midnight version 2
* Nanopore SISPA

(note - the exception is that the lineage BA.1 sample is does not have Nanopore
ARTIC 4.1 reads.)


## Truth VCF files

The truth VCF files are in the directory `Truth_VCFs/`, which contains
a project per directory. Each VCF file is named using the `Sample` column from
`accessions.tsv`, for example the VCF file for sample s11511 is called
`s11511.vcf`. In most cases there is one VCF file per sample. However,
there were samples that had a dropped amplicon in Illumina but not Nanopore
reads (or vice-versa). These samples have a VCF file for each technology,
for example `s32219.Illumina.vcf`, `s32219.Nanopore.vcf`.


## Truth VCF file methods

All reads were mapped to the reference genome using minimap2 (using the
option `-x map-ont` or `-x sr` depending on the sequencing technology), and
then turned into a BAM file using SAMtools.

Variants were then called for each sample by manually inspecting all the reads
mapped to the reference genome using Artemis. Particular attention was paid
to primer regions. Specifically, reads ending in primer sequences were
not trusted, removing false mixed SNP calls.

Where different technologies disagreed at the same position in a given sample,
the position was flagged as "unsure", and is then handled appropriately by
`covid-truth-eval`. The only exception to this is where one technology had
a dropped amplicon, but another technology did not have it dropped - in this
case two separate VCF files were made for that sample (as described earlier).

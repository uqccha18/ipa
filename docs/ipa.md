# Improved Phased Assembler

## Description

Documentation for running the PacBio Improved Phased Assembler (IPA) pipeline for *de novo* genome assembly of HiFi sequencing data.

Purge_Dups is included here as the assemblies produced by IPA are larger than expected, requiring removal of contig overlaps and haplotigs.

**Note:** The IPA workflow creates a Purge_dups output folder but the README clarifies that this step is skipped, so Purge_Dups needs to be run afterwards. Install and run instructions are provided for [Pawsey Nimbus infrastructure](infrastructure_optimisation_nimbus.md) only.

Appropriate links (to BioCommons and/or external documentation) are included for each of the component tools below, *if known*.

| Workflow element | GitHub | bio.tools | BioContainers | bioconda |
|-------------|:--------:|:--------:|:--------:|:--------:|
|Improved Phased Assembler (IPA) | [&#9679;](https://github.com/PacificBiosciences/pbbioconda/wiki/Improved-Phased-Assembler) | | [&#9679;](https://biocontainers.pro/#/tools/pbipa) | [&#9679;](https://anaconda.org/bioconda/pbipa) |
|Purge_Dups | [&#9679;](https://github.com/dfguan/purge_dups) | [&#9679;](https://bio.tools/purge_dups) | | |

### Required (minimum) inputs/parameters

List minimum required inputs & parameters for tool execution here.

- CCS input file (e.g. movie.ccs.bam) - see [IPA documentation](https://github.com/PacificBiosciences/pbbioconda/wiki/Improved-Phased-Assembler) for list of accepted input file types
- Number of threads and Number of jobs - e.g. 64 cores should be split into 4 jobs with 16 threads each

### Third party tools / dependencies

Any additional notes for tool dependencies

- htslib
- libgcc-ng
- networkx
- nim-falcon
- pbmm2
- pcre
- racon
- samtools
- snakemake-minimal
- zlib


## Diagram

**IPA diagram not available**: steps below sourced *verbatim* from the [pbipa repository](https://github.com/PacificBiosciences/pbipa).

1. Building the SeqDB and SeedDB from the input reads.
2. Overlapping using the Pancake overlapper.
3. Phasing the overlaps using the Nighthawk phasing tool.
4. Filtering the overlaps using Falconc m4Filt.
5. Contig construction using Falcon's ovlp_to_graph and graph_to_contig tools.
6. Read tracking for read-to-contig assignment.
7. Polishing using Racon.

**[Purge_Dups diagram](https://github.com/dfguan/purge_dups#overview)**

## Usage

### Summary

| Workflow Tool | Version | Scheduler | Workflow manager | Container | Install method |
|---------------|---------|-----------|------------------|-----------|----------------|
| IPA           |[1.1.2](https://github.com/PacificBiosciences/pbipa/tree/v1.1.2)| See Snakemake documentation | Snakemake | No | Conda |
| Purge_Dups    |1.2.3| NA | NA | No | Manual | 

### High level resource usage

| Tool/WF | Version | Group | Organism name | Genus species | Genome size (GB) | Hours required | Cores | Peak RAM in GB (requested) | Drive (GB) | HPC-HTC | Month-Year |
|---------|---------|-------|---------------|---------------|------------------|----------------|-------|----------------------------|---------------|---------|------------|
|IPA |[1.1.2](https://github.com/PacificBiosciences/pbipa/tree/v1.1.2)|Oz Mammals Genomics|Bilby|Macrotis lagotis|~3.3|3|64|~150 (256)|500|[Nimbus @ Pawsey](infrastructure_optimisation_nimbus.md)|08-2020|
|IPA |[1.1.2](https://github.com/PacificBiosciences/pbipa/tree/v1.1.2)|AWGG|Woylie|Bettongia penicillata|~3.3|3|64|~150 (256)|500|[Nimbus @ Pawsey](infrastructure_optimisation_nimbus.md)|08-2020|
|IPA |NA|Oz Mammals Genomics|Bilby|Macrotis lagotis|~3.3|5|28|~24 (NA)|NA|[Zeus @ Pawsey](infrastructure_optimisation_zeus.md)|08-2020|

### Additional notes

- [Wiki: run modes](https://github.com/PacificBiosciences/pbbioconda/wiki/Improved-Phased-Assembler#ipa-run-modes) (including workflow manager and cluster submission)

## Install

- [Install from BioConda (PacBiosciences)](https://github.com/PacificBiosciences/pbipa#installation-from-bioconda)
- Note: If Samtools fixes from above link do not work you can try running `conda install samtools=1.9 --force-reinstall`
- See infrastructure documentation for specific install instructions:
     - [Zeus @ Pawsey Supercomputing Centre](infrastructure_optimisation_zeus.md) 
     - [Nimbus @ Pawsey Supercomputing Centre](infrastructure_optimisation_nimbus.md)

## Tutorials

- See the following infrastructure links for tutorials:
     - [Zeus @ Pawsey Supercomputing Centre](infrastructure_optimisation_zeus.md) 
     - [Nimbus @ Pawsey Supercomputing Centre](infrastructure_optimisation_nimbus.md)

## Help / FAQ / Troubleshooting

- [Wiki: Quick start guide](https://github.com/PacificBiosciences/pbbioconda/wiki/Improved-Phased-Assembler#quick-start)
- [Wiki: Common solutions](https://github.com/PacificBiosciences/pbbioconda/wiki/Improved-Phased-Assembler#common-solutions)

## Licence(s)

- [Improved Phased Assembler](https://github.com/PacificBiosciences/pbipa/blob/master/LICENSE.txt)
- [Purge_Dups](https://github.com/dfguan/purge_dups/blob/master/LICENSE)

## Acknowledgements / citations / credits

### Workflow components
This workflow documentation refers to, and makes use of, IPA and Purge_Dups tools, which were developed by others. Please see the documentation for the individual tools for further information regarding acknowledgements and citations.

### Community
Infrastructure deployment, optimisation and testing information is provided by the Australasian Wildlife Genomics Group, The University of Sydney and was supported by the University of Sydney and the Pawsey Supercomputing Centre.

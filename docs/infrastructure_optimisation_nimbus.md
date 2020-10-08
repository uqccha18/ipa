# IPA on Nimbus @ Pawsey Supercomputing Centre

## Accessing workflow

The scripts for using [IPA](./IPA.html) on Nimbus, have been made available below in the Quick start tutorial.

## Quick start tutorial

Below is a tutorial for installing and running IPA on Nimbus.

### Install the improved phased assembler via bioconda

**Note:** 

- the instructions below will install the latest version of miniconda and IPA.
- to [install a specific version](https://docs.anaconda.com/anaconda/user-guide/tasks/install-packages/) of IPA, use the following install script: `conda install pbipa=1.1.2`
    
1. Download miniconda: `curl -O https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh`
2. Install miniconda: `sh Miniconda3-latest-Linux-x86_64.sh`
    * **Note**: You can choose where to install Miniconda during the installation steps. Depending on how many programs and dependencies you need to install, the installation folder can get quite large in size so it is recommended to install Miniconda on your attached storage drive rather than on the root drive.
3. Create a conda environment and add the required channels: `conda create -n ipa`
4. Activate the conda environment: `conda activate ipa`
5. Add the required channels: `conda config --add channels defaults && conda config --add channels bioconda && conda config --add channels conda-forge`
6. Install the improved phased assembler in the environment: `conda install pbipa`
7. Verify the installation: `ipa validate`
    * **Note**: If there is a samtools error during installation you may need to run `conda install samtools=1.9 --force-reinstall`

### Install Purge_Dups and required dependencies:

8. Inside the ipa conda environment install minimap 2: `conda install minimap2`
9. Install purge dups:

    ``` bash
    wget https://github.com/dfguan/purge_dups/archive/master.zip
    unzip master.zip
    cd purge_dups-master/src && make
    export PATH=$PATH:/path/to/purge_dups-master/bin/
    ```

### Run the separate elements of the workflow

**Note** The `nohup` command is used to run commands in the background so any interruption to the terminal session will not result in the pipeline being stopped. Feel free to use `screen` or any other method instead. 

#### Improved Phased Assembler

1. Create an output directory on your attached storage drive: `mkdir species_ipa_out`
2. Create a temporary directory on your attached storage drive: `mkdir species_ipa_temp`
3. Run the pipeline in the background: 

    ``` bash
    nohup ipa local --nthreads 16 --njobs 4 --run-dir /path/to/species_ipa_out/ --tmp-dir /path/to/species_ipa_temp/ -i /path/to/input.ccs.bam > species_ipa.log 2>&1&
    ```
    
  * **Note**: To resume an existing run, run the exact same command with `--resume` on the end. See the IPA documentation for a full list of accepted input CCS file types.

#### Purge_Dups

4. Convert ccs.bam file to fastq.gz: 

    ``` bash
    nohup samtools bam2fq -0 multiple_movies.ccs.fastq.gz -T np,rq -@ 64 multiple_movies.ccs.bam > bam2fq.out 2>&1&
    ```

5. Align PacBio fastqs to hifi assembly: 

    ``` bash
    nohup minimap2 -t 64 -xmap-pb -I 6G final.p_ctg.fasta multiple_movies.ccs.fastq.gz | gzip -c - > multiple_movies.ccs.paf.gz 2> align.log &
    ```

  * **Note**: Ensure -I is set to a number larger than the size of your hifi assembly in Gb

6. Create required outputs for purge_dups:

    ``` bash
    pbcstat multiple_movies.ccs.paf.gz > pbcstat.log 2>&1
    calcuts PB.stat > cutoffs 2> calcults.log
    ```

7. Split assembly into contigs and perform self alignment:

    ``` bash
    split_fa final.p_ctg.fasta > final.p_ctg.fasta.split 2> split.log
    nohup minimap2 -t 64 -xasm5 -DP final.p_ctg.fasta.split final.p_ctg.fasta.split | gzip -c - > final.p_ctg.fasta.split.self.paf.gz 2> selfalign.log &
    ```

8. Identify duplications: `nohup purge_dups -2 -T cutoffs -c PB.base.cov final.p_ctg.fasta.split.self.paf.gz > dups.bed 2> purge_dups.log &`
9. Create purged assembly: `nohup get_seqs dups.bed final.p_ctg.fasta > get_seqs.log 2>&1&`
10. Merge the purged haplotigs with the original ipa alternate haplotigs: `cat final.a_ctg.fasta hap.fa > alternate.fa`
11. Perform steps 5-9 on the alternate assembly to get the final set of alternate haplotigs

## Optimisation required

One extra conda command was required to fix an issue with Samtools dependency during IPA install: try running `conda install samtools=1.9 --force-reinstall`

## Infrastructure specifications

## Infrastructure usage and benchmarking

### Summary

### Bilby & Woylie on Nimbus

![](./ipa_nimbus.png)

## Acknowledgements / citations / credits

### Workflow components
This workflow documentation refers to, and makes use of, IPA and PurgeDups tools, which were developed by others. Please see the documentation for the individual tools for further information regarding acknowledgements and citations.

### Community
Infrastructure deployment, optimisation and testing information is provided by the Australasian Wildlife Genomics Group, The University of Sydney and was supported by the University of Sydney and the Pawsey Supercomputing Centre.


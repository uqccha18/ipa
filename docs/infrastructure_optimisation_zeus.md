# IPA on Zeus @ Pawsey Supercomputing Centre

## Accessing workflow

The scripts for using [IPA](./IPA.html) on Zeus have been made available below in the Quick start tutorial.

## Quick start tutorial

Below is a tutorial for install and annotation of IPA on Zeus.

**Note:** Purge_Dups is not included below as it requires a manual install. Documentation is currently not available for this, but may be in the future.

### Install the improved phased assembler via bioconda

**Note:** 

- the instructions below will install the latest version of miniconda and IPA.
- to [install a specific version](https://docs.anaconda.com/anaconda/user-guide/tasks/install-packages/) of IPA, use the following install script: `conda install pbipa=1.1.2`

**It is recommended to use an interactive session on zeus to install IPA. To launch an interactive session use:** `>salloc -n 1 -t 1:00:00`
    
1. Download miniconda: `curl -O https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh`
2. Install miniconda: `sh Miniconda3-latest-Linux-x86_64.sh`
    * **Note**: During the setup steps you will be asked where to install Miniconda. DO NOT install under `$HOME`, the suggested directory for miniconda is  `/group/<project>/<user>/miniconda3`. Whenever you need to use miniconda from the command line you will need to run `export PATH=$PATH:/group/.../miniconda3/bin`, and in some cases also run `exec bash`
3. Create a conda environment and add the required channels: `conda create -n ipa`
4. Activate the conda environment: `conda activate ipa`
5. Add the required channels: `conda config --add channels defaults && conda config --add channels bioconda && conda config --add channels conda-forge`
6. Install the improved phased assembler in the environment: `conda install pbipa`
7. Verify the installation: `ipa validate`
    * **Note**: If there is a samtools error during installation you may need to run `conda install samtools=1.9 --force-reinstall`

### Script

This is an example script for submission to Zeus, based on the Bilby exemplar below. 

**Note:** DO NOT forget to update the `{workdir}` and `{species}` fields in the script, as well as project specific paths and SBATCH directives. Since IPA requires high I/O, all outputs should be written to the locally attached storage on the node (i.e. the /tmp folder). Output files can then be copied back to project directories upon completion (refer to example script). 

    #!/bin/bash --login
    #SBATCH --partition=workq
    #SBATCH --job-name=ipa
    #SBATCH --account=accountID
    #SBATCH --nodes=1
    #SBATCH --tasks-per-node=1
    #SBATCH --cpus-per-task=28
    #SBATCH --time=24:00:00
    #SBATCH --export=NONE

    ## ENSURE TO CHECK AND MODIFY REQUIRED PATHS TO REFLECT YOUR USER SETUP
    
    export OMP_NUM_THREADS=28
    
    export PATH=$PATH:/group/project/user/miniconda3/bin
    
    eval "$(conda shell.bash hook)"
    conda activate ipa
    
    echo "Running on Node $(hostname)"
    
    workdir="/tmp/${SLURM_JOBID}"
    mkdir ${workdir}
    
    cd ${workdir}
    
    echo "Running in Directory ${workdir}"
    
    species="bilby"
    mkdir ${species}_ipa_out
    mkdir ${species}_ipa_tmp
    
    cp /path/to/multiple_movies.ccs.bam .
    
    srun --export=all -n 1 -c 28 ipa local --nthreads 28 --njobs 1 -i multiple_movies.ccs.bam --run-dir ${workdir}/${species}_ipa_out/ --tmp-dir ${workdir}/${species}_ipa_tmp/
    
    cp -r ${species}_ipa_out/ /scratch/project/user/
    
    cd ..
    
    rm -r ${workdir}

## Optimisation required

* Installing miniconda to the project directory instead of default directory
* Force reinstalling samtools to fix Samtools dependency error `conda install samtools=1.9 --force-reinstall`
* Running IPA pipeline on locally attached node storage (i.e. in the `/tmp` directory) and then copying all outputs back due to high I/O demands

## Infrastructure specifications

## Infrastructure usage and benchmarking

### Summary

### Bilby on Zeus

- PacBio HiFi Coverage = ~15x
- CCS Step completed by sequencing company - Input movie.ccs.bam file = ~30GB
- Improved Phased Assembler:
     * Installed via Miniconda using Zeus interactive queue
     * ~5 hours walltime in Zeus longq
     * Max RAM usage ~24GB 


## Acknowledgements / citations / credits

### Workflow components
This workflow documentation refers to and makes use of IPA, which was developed by others. Please see the documentation for the individual tools for further information regarding acknowledgements and citations.

### Community
Infrastructure deployment, optimisation and testing information is provided by the Australasian Wildlife Genomics Group, The University of Sydney and was supported by the University of Sydney and the Pawsey Supercomputing Centre.

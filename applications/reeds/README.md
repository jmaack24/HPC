# Using ReEDS on Eagle

## First Time Setup

This guide explains how to get ReEDS-2.0 running on NREL's HPC platform Eagle.

### Prerequisites
If you are new to HPC environments, you may wish to first visit [NREL's HPC website](https://www.nrel.gov/hpc/index.html).  Pay particular attention to the links in the "Getting Started" box.

ReEDs relies heavily on GAMS (General Algebraic Modeling Language).  GAMS is available on Eagle through the GAMS module:
``
module load gams
``
However, **to use GAMS, you must belong to the appropriate user group**.  In order to be added to the appropriate group, you need to [contact HPC support](https://www.nrel.gov/hpc/contact-us.html) and request access to GAMS.  It normally takes about a day for the HPC support team to process this request.  If you do not have a user account on Eagle, you will first need to [get a user account](https://www.nrel.gov/hpc/user-accounts.html).  

Once you have been added to the user group, you can test by access by typing:
```
module load gams
gams
```

### Installation

1. We first need to load the appropriate modules:
```
module load conda
module load gams
```
2. ReEDS-2.0 requires `git-lfs` to clone the repository.  Using `conda` is the easiest way to install.  We create a new conda environment, activate it and add the lfs extension to git:
```
conda create --name git -c conda-forge git git-lfs
conda activate git
git lfs install
```
3. Next clone the ReEDS-2.0 git repository (this may take a few minutes since the repo contains a large number of files and some of them are large):
```
git clone https://github.nrel.gov/ReEDS/ReEDS-2.0.git
```
4. Now we create the ReEDS conda environment and activate it.  To do this, we will use the `environment.yml` file from the repository:
```
cd ReEDS-2.0
conda env create -f environment.yml
conda activate reeds
```
5. Finally, we will install the required `R` packages (`R` itself was installed in the conda environment).  There are a lot of packages to install so this will take awhile (normally about an hour). A convenient script is in the repository for this:
```
Rscript input_processing/R/packagesetup.R
```

ReEDS should now be usable in from the command line using the python script `runbatch.py`.

## Running

Running ReEDS in the normal way can be done.  In order to  run ReEDS in this way, we need to get a compute node on Eagle.  To do this, login to Eagle and request a compute node (for an hour):
```
srun --time=60 --account=<allocation> --pty $SHELL
```
where `<allocation>` should be replaced with the resource allocation under which your computation will be run.  It may take a few minutes for a compute node to be allocated to you.  For more information, see the HPC Team's guide to [running interactive jobs on Eagle](https://www.nrel.gov/hpc/eagle-interactive-jobs.html).

To run longer jobs, it is best to run batch jobs.  This can be done by setting the `hpc` flag at the end of the file `cases.csv` to `1`.  For sequential runs, the python script uses the batch script template `srun_template.sh` to submit jobs to the short queue (one per case).  For intertemporal runs, the python script uses the batch script template `srun_template_int.sh` to submit jobs to the standard queue (one per case).  In both cases, the only addition to the file is to append
```
sh runs/<my-case>/call_<my-case>.sh
```
to the end of the file.  For details on the meaning of the various flags in the template files, see the HPC Team's website on [running batch jobs](https://www.nrel.gov/hpc/eagle-batch-jobs.html).  Other example batch scripts are also available [here](https://www.nrel.gov/hpc/eagle-sample-batch-script.html).

> Written with [StackEdit](https://stackedit.io/).

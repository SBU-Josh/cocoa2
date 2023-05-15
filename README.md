# Table of contents
1. [Overview of the Cobaya-CosmoLike Joint Architecture (Cocoa)](#overview)
2. [Special Instructions for the SBU supercomputer](#sbu_overview) 
    1. [Using Miniconda](#sbu_overview_anaconda)  
3. [Installation of Cocoa's required packages](#required_packages)
    1. [Via Conda](#required_packages_conda)
4. [Installation of Cobaya base code](#cobaya_base_code)
5. [Running Cobaya Examples](#cobaya_base_code_examples)
6. [Running Cosmolike projects](#running_cosmolike_projects)
7. [Creating Cosmolike projects](#creating_cosmolike_projects)
8. [Appendix](#appendix)
    1. [Proper Credits](#appendix_proper_credits)
    1. [Compiling Boltzmann, CosmoLike and Likelihood codes separatelly](#appendix_compile_separatelly)
    2. [Running Jupyter Notebooks inside the Whovian-Cosmo docker container](#appendix_jupyter_whovian)
    3. [Summary Information about Cocoa's configuration files](#appendix_config_files)
9. [The projects folder (external readme)](https://github.com/SBU-UNESP-2022-COCOA/cocoa2/tree/main/Cocoa/projects)
10. [Adapting new modified CAMB/CLASS (external readme)](https://github.com/SBU-UNESP-2022-COCOA/cocoa2/tree/main/Cocoa/external_modules/code)
 
## Overview of the [Cobaya](https://github.com/CobayaSampler)-[CosmoLike](https://github.com/CosmoLike) Joint Architecture (Cocoa) <a name="overview"></a>

Cocoa allows users to run [CosmoLike](https://github.com/CosmoLike) routines inside the [Cobaya](https://github.com/CobayaSampler) framework. [CosmoLike](https://github.com/CosmoLike) can analyze data primarily from the [Dark Energy Survey](https://www.darkenergysurvey.org) and simulate future multi-probe analyses for LSST and Roman. Besides integrating [Cobaya](https://github.com/CobayaSampler) and [CosmoLike](https://github.com/CosmoLike), this project introduces shell scripts and readme instructions that allow users to containerize [Cobaya](https://github.com/CobayaSampler). The container structure ensures that: 
1. Users will adopt the same compiler and libraries (including their versions). 
2. Users will be able to use multiple [Cobaya](https://github.com/CobayaSampler) instances consistently. 

This readme file presents basic and advanced instructions for installing all [Cobaya](https://github.com/CobayaSampler) and [CosmoLike](https://github.com/CosmoLike) components.

## Special Instructions for the SBU supercomputer <a name="sbu_overview"></a>

### Using Miniconda <a name="sbu_overview_anaconda"></a>

(**warning**) Make sure you don't have system anaconda loaded via the command 

    module unload anaconda

Download and run Miniconda installation script (please adapt `CONDA_DIR`):

    export CONDA_DIR=/gpfs/home/vinmirandabr/miniconda

    mkdir $CONDA_DIR

    wget https://repo.continuum.io/miniconda/Miniconda3-py37_4.8.3-Linux-x86_64.sh

After installation, users must source conda configuration file, see the line below (add such line to your $.bashrc$ file)

    source $CONDA_DIR/etc/profile.d/conda.sh

When running conda for the first time, use the instructions below to configure the use of channels

    conda config --set auto_update_conda false 
    conda config --set show_channel_urls true 
    conda config --set auto_activate_base false 
    conda config --prepend channels conda-forge 
    conda config --set channel_priority strict 
    conda init bash
    
Users can now go to section [Installation of Cocoa's required packages via conda](#required_packages_conda). 

## Installation of Cocoa's required packages <a name="required_packages"></a>

[CosmoLike](https://github.com/CosmoLike) and [Cobaya](https://github.com/CobayaSampler) require many C, C++ and Python packages to be installed as prerequisites. The overabundance of compiler and package versions, each with a different set of bugs and regressions, complicate the installation of Cocoa in HPC environments and the verification of numerical results. This section standardize the package environment.

### Via Conda (best for Linux/HPC) <a name="required_packages_conda"></a>

The more straightforward way to install most prerequisites is via [Conda](https://github.com/conda/conda). Cocoa's internal scripts will then install any remaining missing packages, using a provided internal cache located at [cocoa_installation_libraries](https://github.com/SBU-Josh/cocoa2/tree/main/cocoa_installation_libraries). Assuming that the user had previously installed [Minicoda](https://docs.conda.io/en/latest/miniconda.html) (or [Anaconda](https://www.anaconda.com/products/individual)), the first step is to type the following commands to create the cocoa Conda environment.

    conda create --name cocoa python=3.7 --quiet --yes \
      && conda install -n cocoa --quiet --yes  \
      'conda-forge::libgcc-ng=10.3.0' \
      'conda-forge::libstdcxx-ng=10.3.0' \
      'conda-forge::libgfortran-ng=10.3.0' \
      'conda-forge::gxx_linux-64=10.3.0' \
      'conda-forge::gcc_linux-64=10.3.0' \
      'conda-forge::gfortran_linux-64=10.3.0' \
      'conda-forge::openmpi=4.1.1' \
      'conda-forge::sysroot_linux-64=2.17' \
      'conda-forge::git=2.33.1' \
      'conda-forge::git-lfs=3.0.2' \
      'conda-forge::hdf5=1.10.6' \
      'conda-forge::git-lfs=3.0.2' \
      'conda-forge::cmake=3.21.3' \
      'conda-forge::boost=1.76.0' \
      'conda-forge::gsl=2.7' \
      'conda-forge::fftw=3.3.10' \
      'conda-forge::cfitsio=4.0.0' \
      'conda-forge::openblas=0.3.18' \
      'conda-forge::lapack=3.9.0' \
      'conda-forge::armadillo=10.7.3'\
      'conda-forge::expat=2.4.1' \
      'conda-forge::cython=0.29.24' \
      'conda-forge::numpy=1.21.4' \
      'conda-forge::scipy=1.7.2' \
      'conda-forge::pandas=1.3.4' \
      'conda-forge::mpi4py=3.1.2' \
      'conda-forge::matplotlib=3.5.0' \
      'conda-forge::astropy=4.3.1' \
      && conda run -n cocoa /opt/conda/envs/cocoa/bin/pip install --no-cache-dir --root-user-action=ignore \
        'tensorflow-cpu==2.8.0' \
        'keras==2.8.0' \
        'keras-preprocessing==1.1.2' \
        'torch==1.11.0+cpu' \ 
        'torchvision==0.12.0+cpu' -f https://download.pytorch.org/whl/torch_stable.html

(**expert**) We added tensorflow, keras and torch for future emulator projects.

With this installation method, users must activate the Conda environment whenever working with Cocoa, as shown below 

    $ conda activate cocoa
    
When loading conda cocoa environment for the first time, users must install git-lfs

    $(cocoa) $CONDA_PREFIX/bin/git-lfs install

**Users can now proceed to the section [Installation of Cobaya base code](#cobaya_base_code)** 
   
(**expert**) Our scripts never install packages on `$HOME/.local`. Doing so could impose incompatibilities between Cobaya and different projects (or break the user's environment for other projects). All requirements for Cocoa are installed at

    Cocoa/.local/bin
    Cocoa/.local/include
    Cocoa/.local/lib
    Cocoa/.local/share

**Users can now proceed to the section [Installation of Cobaya base code](#cobaya_base_code)**

## Installation of Cobaya base code <a name="cobaya_base_code"></a>

Type:

    $(cocoa) $CONDA_PREFIX/bin/git-lfs clone https://github.com/SBU-Josh/cocoa2.git

to clone the repository. 

(**expert**) Cocoa developers with set ssh keys  in GitHub may find more convenient to use the command

    $(cocoa) $CONDA_PREFIX/bin/git-lfs clone git@github.com:SBU-Josh/cocoa2.git

(**Warning**) We assumed in the command above users have installed the pre-requisite packages (including git-lfs) via the recommended **Conda installation method**. With other installation method, `$CONDA_PREFIX/bin/git-lfs` should be replaced with `git-lfs`. 

(**Warning**) We have a limited monthly quota in bandwidth for [Git LFS](https://git-lfs.github.com) files, and therefore we ask users to use good judgment in the number of times they clone Cocoa's main repository. 

Cocoa is made aware of the chosen installation method of required packages via special environment keys located on the [set_installation_options](https://github.com/SBU-Josh/cocoa2/blob/main/Cocoa/set_installation_options) script (located at Cocoa/ subdirectory), as shown below

    [Extracted from set_installation_options script]
    #  ---------------------------------------------------------------------------
    # HOW COCOA BE INSTALLED? -------------------------------

    #export DOCKER_INSTALLATION=1
    #export MINICONDA_INSTALLATION=1
    #export MACOS_HOMEBREW_INSTALLATION=1
    #export MANUAL_INSTALLATION=1
    
The user must uncomment the appropriate key, and then type the following command

    $ source setup_cocoa_installation_packages

This script decompress the data files and install all packages that may have been left out in the Conda/Docker/Manual installation. 

(**expert**) File decompression should only take a few minutes, while package installation time may range from a few minutes (if installation via *Conda* or *Docker* was selected) to more than one hour (if installation *via Cocoa's internal scripts and cache* was selected). 

Finally, type

    $ source compile_external_modules
    
to compile CAMB, CLASS, Planck and Polychord.

(**expert**) If the user wants recompile just one of these packages, read the appendix [Compiling Boltzmann, CosmoLike and Likelihood codes separatelly](#appendix_compile_separatelly).

## Running Cobaya Examples <a name="cobaya_base_code_examples"></a>

Assuming the user opted for the easier *Conda installation* and located the terminal at the folder *where Cocoa was cloned*, this is how to run some example YAML files we provide (*no Cosmolike code involved*): 

**Step 1 of 5**: activate the conda environment

    $ conda activate cocoa
     
**Step 2 of 5**: go to the Cocoa main folder 

    $(cocoa) cd ./cocoa/Cocoa

**Step 3 of 5**: activate the private python environment

    $(cocoa) source start_cocoa

(**warning**) Users will see a terminal that looks like this: `$(Cocoa)(.local)`. *This is a feature, not a bug*! 

(**expert**) Why `$(Cocoa)(.local)` is a feature, not a bug? The Conda environment can be the same for all Cocoa instances, with [start_cocoa](https://github.com/SBU-Josh/cocoa2/blob/main/Cocoa/start_cocoa)/[stop_cocoa](https://github.com/SBU-Josh/cocoa2/blob/main/Cocoa/stop_cocoa) loading/unloading the corresponding `LD_LIBRARY_PATH`, `CPATH`, `C_INCLUDE_PATH`, `CPLUS_INCLUDE_PATH` and `PATH`. *Why more than one Cocoa instance?* While users may be running chains in one instance, they might use a second instantiation to make experimental changes (in our experience this happens a lot).

**Step 4 of 5**: select the number of OpenMP cores
    
    $(cocoa)(.local) export OMP_PROC_BIND=close
    $(cocoa)(.local) export OMP_NUM_THREADS=4

**Step 5 of 5**: run `cobaya-run` on a the first example YAML files we provide.

One model evaluation:

     $(cocoa)(.local) mpirun -n 1 --mca btl tcp,self --bind-to core --rank-by core --map-by numa:pe=${OMP_NUM_THREADS} cobaya-run ./projects/example/EXAMPLE_EVALUATE1.yaml -f
     
MCMC:

     $(cocoa)(.local) mpirun -n 4 --mca btl tcp,self --bind-to core --rank-by core --map-by numa:pe=${OMP_NUM_THREADS} cobaya-run ./projects/example/EXAMPLE_MCMC1.yaml -f

(**expert**) Why the `--mca btl tcp,self` flag? Conda-forge developers don't [compile OpenMPI with Infiniband compatibility](https://github.com/conda-forge/openmpi-feedstock/issues/38). Users outraged by the overhead that TCP will bring over Infiniband can perform the [installation via Cocoa's internal cache](#required_packages_cache). 

(**expert**) Why the `--bind-to core --rank-by core --map-by numa:pe=${OMP_NUM_THREADS}` flag? To enable hybrid MPI + OpenMP run at UofA's supercomputer. *Users should check if the flag is necessary on their particular environment.*

Once the work is done, type:

    $(cocoa)(.local) source stop_cocoa

(**expert**) [stop_cocoa](https://github.com/SBU-Josh/cocoa2/blob/main/Cocoa/stop_cocoa) will also restore `OMP_NUM_THREADS` to its original value before the script [start_cocoa](https://github.com/SBU-Josh/cocoa2/blob/main/Cocoa/start_cocoa) was sourced. 

and (optional)  
    
    $(cocoa) conda deactivate cocoa

(**expert**) Why is the deactivation of the cocoa Conda environment flag optional? The Cocoa Conda environment can be helpful in many types of projects!

## Running Cosmolike projects <a name="running_cosmolike_projects"></a> 

The projects folder was designed to include all Cosmolike projects. Like the last section, we assume the user opted for the easier *Conda installation*, and located the terminal at the folder *where Cocoa was cloned*.

**Step 1 of 5**: go to the project folder (`./cocoa/Cocoa/projects`) and clone a Cosmolike project, with fictitious name XXX,  as shown below:

    $ cd ./cocoa/Cocoa/projects
    $ git clone git@github.com:CosmoLike/cocoa_XXX.git XXX

Example of cosmolike projects: [lsst_y1](https://github.com/SBU-UNESP-2022-COCOA/cocoa_lsst_y1), [des_y3](https://github.com/SBU-UNESP-2022-COCOA/cocoa_des_y3).

(**warning**) The Cosmolike Organization hosts a Cobaya-Cosmolike project named XXX at `CosmoLike/cocoa_XXX`. However, our provided scripts and template YAML files assume the removal of the `cocoa_` prefix when cloning the repository.

(**expert**) The prefix `cocoa_` on Cosmolike organization avoids mixing Cobaya-Cosmolike projects with code meant to be run on the legacy CosmoLike code.

**Step 2 of 5**: go back to Cocoa main folder
    
    $ cd ../
    
**Step 3 of 5**: activate conda Cocoa environment and the private python environment

     $ conda activate cocoa
     $(cocoa) source start_cocoa
 
(**warning**): Please run the [start_cocoa](https://github.com/SBU-Josh/cocoa2/blob/main/Cocoa/start_cocoa) script *after* cloning the project repository. 
 
(**expert**) Why the warning above? The script [start_cocoa](https://github.com/SBU-Josh/cocoa2/blob/main/Cocoa/start_cocoa) creates symbolic links, in all available projects, between `./project/XXX/likelihood` and `./cobaya/cobaya/likelihoods/XXX`; `./project/XXX/data` and `./external_modules/data/XXX`; `./project/XXX/interface` and `./external_modules/code/XXX`. It also adds the *Cobaya-Cosmolike interface* of all projects to `LD_LIBRARY_PATH` and `PYTHONPATH` by calling `./projects/XXX/scripts/start_XXX`.

**Step 4 of 5**: compile the project
 
     $(cocoa)(.local) source ./projects/XXX/scripts/compile_XXX
  
 **Step 5 of 5**: select the number of OpenMP cores and run a template yaml file
    
    $(cocoa)(.local) export OMP_NUM_THREADS = 4
    $(cocoa)(.local) mpirun -n 1 --mca btl tcp,self --bind-to core --rank-by core --map-by numa:pe=${OMP_NUM_THREADS} cobaya-run ./projects/XXX/EXAMPLE_EVALUATE1.yaml -f

## Creating Cosmolike projects <a name="creating_cosmolike_projects"></a> 

The `XXX` project needs to have more or less the following structure (taken from our private DES-Y3 project)

    +-- cocoa_des_y3
    |    +-- likelihood
    |    |   +-- _cosmolike_prototype_base.py
    |    |   +-- des_3x2pt.py
    |    |   +-- des_3x2pt.yaml
    |    |   +-- des_2x2pt.py
    |    |   +-- des_3x2pt.yaml
    |    |   +-- des_cosmic_shear.py
    |    |   +-- des_cosmic_shear.yaml
    |    +-- scripts
    |    |   +-- compile_des_y3
    |    |   +-- start_des_y3
    |    |   +-- stop_des_y3
    |    +-- data
    |    |   +-- DES.paramnames
    |    |   +-- DES_Y3.dataset
    |    |   +-- datavector.txt
    |    |   +-- covariance.txt
    |    |   +-- nzlens.txt
    |    |   +-- nzsource.txt
    |    |   +-- mask.mask
    |    +-- interface
    |    |   +-- MakefileCosmolike
    |    |   +-- cosmolike_des_y3_interface.py
    |    |   +-- interface.cpp
    |    |   +-- interface.hpp
    |    +-- chains
    |    |   +-- README

We expect to have a public project that can be used as a template for new Cosmolike projects in the near future.

## Appendix <a name="appendix"></a>

### Proper Credits <a name="appendix_proper_credits"></a>

The following is not an exhaustive list of the codes we use

- [Cobaya](https://github.com/CobayaSampler) is a framework developed by Dr. Jesus Torrado and Prof. Anthony Lewis

- [Cosmolike](https://github.com/CosmoLike) is a framework developed by Prof. Elisabeth Krause and Prof. Tim Eifler

- [CAMB](https://github.com/cmbant/CAMB) is a Boltzmann code developed by Prof. Anthony Lewis

- [CLASS](https://github.com/lesgourg/class_public) is a Boltzmann code developed by Prof. Julien Lesgourgues and Dr. Thomas Tram

- [Polychord](https://github.com/PolyChord/PolyChordLite) is a sampler code developed by Dr. Will Handley, Prof. Lasenby, and Prof. M. Hobson

By no means, we want to discourage people from cloning code from their original repositories. We've included these codes as compressed [xz file format](https://tukaani.org/xz/format.html) in our repository for convenience in the initial development. The work of those authors is extraordinary, and they must be properly cited.

### Compiling Boltzmann, CosmoLike and Likelihood codes separatelly <a name="appendix_compile_separatelly"></a>

To avoid excessive compilation times during development, users can use following specialized scripts that compile only the specific modules:

    $(cocoa)(.local) source ./installation_scripts/setup_class

    $(cocoa)(.local) source ./installation_scripts/setup_camb

    $(cocoa)(.local) source ./installation_scripts/setup_planck

    $(cocoa)(.local) source ./installation_scripts/setup_polychord

Here we assumed that Cocoa's private python environment, `(.local)`, was already set.

### Summary Information about Cocoa's configuration files <a name="appendix_config_files"></a>

The installation of Cocoa required packages, as well as Boltzmann and Likelihood codes, are managed via the following scripts located at `./Cocoa`.

 - [set_installation_options](https://github.com/SBU-Josh/cocoa2/blob/main/Cocoa/set_installation_options)

    This file contains environment variables that manage the installation process.

 - [setup_cocoa_installation_packages](https://github.com/SBU-Josh/cocoa2/blob/main/Cocoa/setup_cocoa_installation_packages)

    This file has instructions on how to install packages required by the Cocoa Framework.

 - [compile_external_modules](https://github.com/SBU-Josh/cocoa2/blob/main/Cocoa/compile_external_modules)

    This file has instructions on how to compile Boltzmann, Sampler and likelihood codes. 

 - [start_cocoa](https://github.com/SBU-Josh/cocoa2/blob/main/Cocoa/start_cocoa)

    This file has instructions on how to set up the Python virtual environment.

 - [stop_cocoa](https://github.com/SBU-Josh/cocoa2/blob/main/Cocoa/stop_cocoa)

    This file has instructions on how to unset the Python virtual environment - including recovering original `PYTHONPATH`, `LD_LIBRARY_PATH`, and `PATH`. 

 - [clean_all](https://github.com/SBU-Josh/cocoa2/blob/main/Cocoa/clean_all)

    This file has instructions on how to clean keys associated with the Python virtual environment and delete the compilation of the Boltzmann, Sampler, and likelihood codes, and local installation of the required packages installed by the [setup_cocoa_installation_packages].

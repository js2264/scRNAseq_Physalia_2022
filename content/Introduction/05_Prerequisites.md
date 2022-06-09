---
title: "5. Prerequisites and required config"
---

## Prerequisites

The course is intended for those who have basic familiarity with Unix and the R scripting language.

We will also assume that you are familiar with mapping and analysing bulk RNA-seq data as well as with the commonly available computational tools.

* If a refresher is needed for Unix command line (hopefully not), please go over [this tutorial](https://ryanstutorials.net/linuxtutorial/) and its [companion cheatsheet](https://ryanstutorials.net/linuxtutorial/cheatsheet.php).
* Getting down to basics: an introduction to the fundamentals of R ([courtesy of Mark Ravinet](markravinet.github.io/Introduction.html)).
* Gentle introduction to `R/Biocondutor`: [here](https://bioconductor.github.io/BiocWorkshops/introduction-to-bioconductor-annotation-resources.html)
* For a full in-depth guide of `Bioconductor` ecosystem: read the comprehensive `R/Bioconductor` book from Kasper D. Hansen available under the CC BY-NC-SA 4.0 license [[PDF]](/{{<myPackageUrl>}}docs/bioconductor.pdf)

## Local configuration 

* Ideally (though not strictly required), a configured SSH client (it should be already installed on Linux/Mac machines, `PuTTY` can be set up for Windows). 
* Ideally (though not strictly required), a SSH ftp client (`Forklift` is excellent for Mac, although not free beyond the trial version; `cyberduck` can be used for Windows; `FileZilla` can be used for both Mac, Windows and Linux).
* Computer with high-speed internet access (no specific configuration required - everything will be performed on a remote AWS machine). 
* Zoom visioconference software

## Remote configuration 

The AWS machine is running `Ubuntu 18.04 LTS` and has been set up as follows (note this is specific to `Ubuntu 18.04`):

```sh
## --- Clean up previous R installs
sudo apt purge r-base* r-recommended r-cran-*
sudo apt autoremove
sudo apt update
sudo apt upgrade

## --- Libraries
sudo apt update
sudo apt install libc6 libicu66 libreadline8 -y 
sudo apt install -y \
    gcc g++ perl python3 python3-pip python-dev \
    automake make cmake less vim nano fort77 \
    wget git curl bsdtar bzip2 gfortran unzip ftp \
    libpng-dev libjpeg-dev \
    texlive-latex-base default-jre build-essential \
    libbz2-dev liblzma-dev libtool \
    libxml2 libxml2-dev zlib1g-dev \
    libdb-dev libglu1-mesa-dev zlib1g-dev  \
    libncurses5-dev libghc-zlib-dev libncurses-dev \
    libpcre3-dev libxml2-dev \
    libblas-dev libzmq3-dev libreadline-dev libssl-dev \
    libcurl4-openssl-dev libx11-dev libxt-dev \
    x11-common libcairo2-dev \
    libreadline6-dev libgsl0-dev \
    libeigen3-dev libboost-all-dev \
    libgtk2.0-dev xvfb xauth xfonts-base \
    apt-transport-https libhdf5-serial-dev \
    libudunits2-dev libgdal-dev libgeos-dev libproj-dev \
    libv8-dev libnode-dev \
    libmagick++-dev \
    libharfbuzz-dev libfribidi-dev

## --- R base install 
sudo add-apt-repository 'deb https://cloud.r-project.org/bin/linux/ubuntu bionic-cran40/'
add-apt-repository "deb https://cloud.r-project.org/bin/linux/ubuntu `lsb_release -cs` -cran40/"
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E298A3A825C0D65DFD57CBB651716619E084DAB9
sudo apt update
sudo apt install r-base r-recommended r-base-core r-base-dev
```

The following packages have been installed (along with their many dependencies, of course!): 

```sh
## --- Install important R packages for single-cell RNA-seq projects
Rscript -e "
## CRAN packages
install.packages('tidyverse')
install.packages('devtools')
install.packages('umap')
install.packages('corrplot')
install.packages('gam')
install.packages('ggbeeswarm')
install.packages('ggthemes')
install.packages('Matrix')
install.packages('zeallot')
install.packages('fossil')
install.packages('rgl', dependencies=TRUE)
install.packages('BiocManager')
install.packages('Seurat')
install.packages('rliger')
install.packages('Signac')

## Bioconductor Packages
BiocManager::install('SingleCellExperiment', update = FALSE)
BiocManager::install('scran', update = FALSE)
BiocManager::install('scater', update = FALSE)
BiocManager::install('batchelor', update = FALSE)
BiocManager::install('DropletUtils', update = FALSE)
BiocManager::install('AUCell', update = FALSE)
BiocManager::install('plyranges', update = FALSE)
BiocManager::install('ggraph', update = FALSE)
BiocManager::install('clustree', update = FALSE)
BiocManager::install('celldex', update = FALSE)
BiocManager::install('SingleR', update = FALSE)
BiocManager::install('slingshot', update = FALSE)
BiocManager::install('tradeSeq', update = FALSE)
BiocManager::install('velociraptor', update = FALSE)
BiocManager::install('BUSpaRse', update = FALSE)
BiocManager::install('org.Mm.eg.db', update = FALSE)
BiocManager::install('org.Hs.eg.db', update = FALSE)
BiocManager::install('destiny', update = FALSE)
BiocManager::install('TENxPBMCData', update = FALSE)
BiocManager::install('scRNAseq', update = FALSE)
BiocManager::install('scDblFinder', update = FALSE)
BiocManager::install('chromVAR', update = FALSE)
BiocManager::install('EnsDb.Hsapiens.v75', update = FALSE)
"

## --- Create scRNAseq2022 conda env. and add other dependencies
sudo R --no-save -e "reticulate::install_miniconda()"
sudo R --no-save -e "reticulate::conda_create(envname = 'scRNAseq2022')"
conda init bash
conda activate scRNAseq2022
conda install -c conda-forge python=3 umap-learn leidenalg -y
conda install -c conda-forge numpy \
    scipy \
    pandas \
    matplotlib \
    setuptools \
    umap-learn

## --- Install other softwares (fastQC, samtools, cellranger and cellranger indexes)
conda install -c bioconda fastqc samtools
cd /opt/
sudo wget -O cellranger-6.0.1.tar.gz "https://cf.10xgenomics.com/releases/cell-exp/cellranger-6.0.1.tar.gz?Expires=1622001486&Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9jZi4xMHhnZW5vbWljcy5jb20vcmVsZWFzZXMvY2VsbC1leHAvY2VsbHJhbmdlci02LjAuMS50YXIuZ3oiLCJDb25kaXRpb24iOnsiRGF0ZUxlc3NUaGFuIjp7IkFXUzpFcG9jaFRpbWUiOjE2MjIwMDE0ODZ9fX1dfQ__&Signature=IWM4eRXo7G4YRXjOrcTFKscHWsa5sU6bqrIFrWOyzkV0pzOb0OPrjI7uMtoorqV6ivIjKbE21F9PkfajqV77hfODzCIyOmVQWxi9~nV2vZ6fdmo80hB4xmFQ7RdmLNAS~MDrhAg8etcQ-VkPS6wbzwtfNai-jDfRaas7DNPcq5CtFA4UBtfMG51XTpfPFKHDt66QWQOKShD5JNi05Cq4mDcWfJD1EFC-Z5b0Nid416NeLtxzUjNl043VRpWk2EibNGn8s8qGO0Kk~5Uh1l-qW~KkLSPVv5QFWj5wAgPjC3At2bCqjBaD6c87lIcHKx7WTPv46-d-gdQvYg-ZRcmBPQ__&Key-Pair-Id=APKAI7S6A5RYOXBWRPDA"
sudo tar -xzvf cellranger-6.0.1.tar.gz
sudo wget https://cf.10xgenomics.com/supp/cell-exp/refdata-gex-mm10-2020-A.tar.gz
sudo tar -xzvf refdata-gex-mm10-2020-A.tar.gz
```



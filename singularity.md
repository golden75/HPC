# Singulariy Containers   

Singulariy container can be built from the scratch or can be pulled from a repo (Docker or singulairy)  


## Buliding a container using a definition file   

Simple definition file will look like:   
```
BootStrap: docker
From: biobakery/humann

%labels
  Maintainer CBC Support
      Email cbcsupport@uconn.edu


%post
  # Setup directories for bind points
  mkdir -p /archive
  mkdir -p /BCTRN
  mkdir -p /isg
  mkdir -p /linuxshare
  mkdir -p /UCHC
  mkdir -p /labs
  mkdir -p /scratch
  mkdir -p /public
  mkdir -p /tools
  mkdir -p /core
  mkdir -p /projects
  mkdir -p /tgc

%runscript
    exec humann "$@"

%apprun humann
    exec humann "$@"

```  
More detailed information on definition file [https://sylabs.io/guides/3.9/user-guide/definition_files.html](https://sylabs.io/guides/3.9/user-guide/definition_files.html) can be found here.   


Command:  
```
module load singularity/3.9.2
module load squashfs/4.3 
singularity build humann.sif humann.sdef 
```  

In the above main program is humann so it can be called using that.   


## Pulling using a singulariy image  

[souporcell](https://github.com/wheaton5/souporcell)  

```
module load singularity/3.9.2 
singularity pull shub://wheaton5/souporcell 
```  

This will create `souporcell_latest.sif` image file. Whcih can be called using: 
```
singularity exec /path-to/souporcell_latest.sif souporcell_pipeline.py
```

if you are seting this in a module file, it can be done by: 

```
set-alias souporcell_pipeline.py "singularity exec /path-to/souporcell_latest.sif souporcell_pipeline.py"
```  

## Pulling from a Docker image  

```
export TMPDIR=/scratch/$USER
export SINGULARITY_TMPDIR=/scratch/$USER

singularity pull metaphlan.sif --dir $OUTDIR docker://biobakery/metaphlan  
```   




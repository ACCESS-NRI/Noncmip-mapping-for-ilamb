# ilamb-noncmip-ACCESS-tutorial

## Background

This documentation is for ilamb users who want to use ACCESS noncmip data as module-result to input to ilamb.

Ilamb is design for CMIP data, so if we want to use non-cmip dataset as input, we need to convert it to cmip format.

## Installation requirements

xarray                    >=2023.2.0

cdms2                     >=3.1.5

numpy                     1.23.5

dask                      2023.6.0

## Use guide
Before you start, make sure you have authority to access group p73 and p66 on NCI, because mapping program need to get non-CMIP data and other support dataset from those two group.

### PBS file structure

There is an example of PBS file:
```
#!/bin/bash

#PBS -N #NAME_OF_JOB
#PBS -l wd
#PBS -P #GROUP_OF_COMPUTING_RESOURCE
#PBS -q #QUEUE
#PBS -l walltime= 
#PBS -l ncpus=
#PBS -l mem=           
#PBS -l jobfs=        
#PBS -l storage=


export ILAMB_ROOT_PARENT= (parent directory of ILAMB_ROOT) 
export ILAMB_ROOT=(ILAMB_ROOT is where the data is)
export CARTOPY_DATA_DIR=(data you need to pre download for data visualization)
export BUILD_DIR=(path to the output of ilamb)
export BUILD_DIR_PARENT=(parent directory of BUILD_DIR)
export CONFIG_DIR=(path to config file)

rm -rf $BUILD_DIR

module load YOUR_MODULE
source activate YOUR_ENVIRONMENT

mpiexec -n 16 ilamb-run --config $CONFIG_DIR --model_setup $PWD/txtset/non_cmip.txt --regions global --build_dir $BUILD_DIR

```

This example demonstrate what PBS attribute need to be specified, what environment variables need to be pre-defined, and how to trigger the ilanb. Only thing which need to notice is the txt file for model_setup, which well be explain in detail later.


### Model setup file format
it's easy to use, once you know how to use --model_setup and txt file to specify the path of model result data for ilamb, for example:

```
bcc-csm1-1          , MODELS/CMIP5/bcc-csm1-1     , CMIP5
CanESM2             , MODELS/CMIP5/CanESM2        , CMIP5
CESM1-BGC           , MODELS/CMIP5/CESM1-BGC      , CMIP5
```

for each line, first part is the name of dataset, second part is the path to dataset, third part is the group of dataset. for non-CMIp data, its the same:

```
HI-C-05-r1,    /g/data/p73/archive/non-CMIP/ACCESS-ESM1-5/HI-C-05-r1,   noncmip
```

First and second part are the same, and the third part must be specify as noncmip cause the program will use this as a flag to trigger the whole dataset mapping process.

### CMORise

For CMORise part we will not explicitly show you because it work autometically when you trigger the ilamb. Which variable will be CMORise is depend on your `config` file, more specificly, it's depend on the `variable` and `alternate_vars` in your `config` file and the `ctype` which is the special `Comfrontation` you may use in ilamb. Once you run the ilamb_noncmip version, it will create a directory named `temp` which will be at the same level of `_build` directory:

```
.
├── _build
└── temp
```

The `temp` directory will contain all CMORised variables stored in NETcdf format:

```
.
├── cSoil.nc
├── cVeg.nc
├── evspsbl.nc
├── gpp.nc
├── hfls.nc
├── hfss.nc
├── hurs.nc
├── lai.nc
├── nbp.nc
├── pr.nc
├── ra.nc
├── rh.nc
├── rlds.nc
├── rlus.nc
├── rsds.nc
├── rsus.nc
├── tasmax.nc
├── tasmin.nc
├── tas.nc
└── tsl.nc
```
For this version we only provide automatical CMOrise for 20 variables above, Which meet the demand of `CMIP6.cfg` provide by ilamb official github repo. 
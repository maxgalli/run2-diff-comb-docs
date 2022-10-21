# Installation

## CMSSW 11_2 (ROOT 6.22)

This is supposed to use the branches called 112x in both Combine and CombineHarvester. At the moment of writing, only the first one is available, hence follow these instructions.

```
export SCRAM_ARCH=slc7_amd64_gcc900
cmsrel CMSSW_11_2_0_pre10
cd CMSSW_11_2_0_pre10/src
cmsenv
git clone -b 112x https://github.com/cms-analysis/HiggsAnalysis-CombinedLimit.git HiggsAnalysis/CombinedLimit
git clone -b root622_comp https://github.com/maxgalli/CombineHarvester.git
scramv1 b
```

Remember that running ```cmsenv``` from inside ```CMSSW_11_2_0_pre10/src``` has to be done every time a new session starts.

In this case, installing the package is a bit more complicated since we don't have privileges on the ```cvmfs``` directories.
To install in a specific directory (e.g. ```~/install_dir```) the process consists in the following:

```
source setup_cmssw112_env.sh full_path_to_install_dir
pip install -e . --prefix=../install_dir
```

or

```
python setup.py install --home=~/install_dir
```

The same can be done with the version of Combine which was used for the STXS combination, where it is possible to use the compiled PDFs bullshit.

```
export SCRAM_ARCH=slc7_amd64_gcc700
cmsrel CMSSW_10_2_13
cd CMSSW_10_2_13/src
cmsenv
git clone -b 102x-comb2021 https://github.com/cms-analysis/HiggsAnalysis-CombinedLimit.git HiggsAnalysis/CombinedLimit
git clone -b comb2021 https://github.com/cms-analysis/CombineHarvester.git 
scramv1 b
```

```
source setup_cmssw102_env.sh full_path_to_install_dir
pip install -e . --prefix=../install_dir
```

### Note about Tier3 PSI

When compiling CombineHarvester within CMSSW, you could get an error related to a command not correctly parsed. Simply run that command by hand and remove it from ```BuildFile.xml```.

## Conda environment

To set up the environment, we use [this branch](https://github.com/cms-analysis/HiggsAnalysis-CombinedLimit/pull/648) (that will probably be in master soon) and follow [these](https://github.com/nsmith-/HiggsAnalysis-CombinedLimit/tree/root6.22-compat#standalone-compilation-with-conda) instructions. This will create a conda environment with Python 2 and install Combine. However, at the moment of writing there is no branch in [CombineHarvester](https://github.com/cms-analysis/CombineHarvester) to install it in a Conda environment.
Installing the package in the ```site-packages``` directories can be done simply with

```
pip install -e .
```

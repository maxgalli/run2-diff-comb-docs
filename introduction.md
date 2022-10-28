# 1 - Introduction


## Repositories

In order to (re)produce the analysis, the following repositories are necessary:

- [main repo](https://gitlab.cern.ch/magalli/DifferentialCombinationRun2)
- [plotting package](https://github.com/maxgalli/DifferentialCombinationPostProcess)
- [EFTModelsStudies](https://github.com/maxgalli/EFTModelsStudies)
- [EFTScalingEquations](https://github.com/maxgalli/EFTScalingEquations/tree/differentials_220506)
- [EFT2Obs](https://github.com/maxgalli/EFT2Obs/tree/Run2Legacy_WithOtherChannels2)
- Combine + CombineHarvester
- an installation of [TK's framework](https://github.com/tklijnsma/differentialCombination2017) would be useful (even if maybe not necessary)

## Analyses

Following are the analyses that are in the combination (or have been at some point) with the link to the repos:

- Hgg: [HIG-19-016](https://gitlab.cern.ch/magalli/hig-19-016/-/tree/for_combination)
- HZZ: [HIG-21-009](https://gitlab.cern.ch/magalli/hig-21-009/-/tree/Run2Combination)
- HWW: [HIG-19-002](https://gitlab.cern.ch/magalli/hig-19-002/-/tree/Run2Combination)
- Htt: [HIG-20-015](https://gitlab.cern.ch/magalli/hig-20-015/-/tree/Run2Combination_renamed_correctnames)
- Hbb: [HIG-19-003](https://gitlab.cern.ch/magalli/hig-19-003)
- HbbVBF: [HIG-21-020](https://gitlab.cern.ch/magalli/hig-21-020/-/tree/DifferentialsRun2)
- HttBoost: [HIG-21-017](https://gitlab.cern.ch/magalli/hig-21-017/-/tree/DifferentialsRun2)

## Preliminary Operations

Here we summarize the prelimiary operations that have to be performed. In principle, all the outputs and results of these operations should already be included in the repo, but with report them here in case there are problems and they have to be repeated.

### Hgg-Envelope
The main problem that emerged with Hgg, especially during the SMEFT interpretation, is the amount of time that the envelope takes to run. It was thus decided to implement a suggestion by Andrea that consists in throwing away the pdfs that are not reasonable within the envelope. This is performed in the following way:

```
cd DifferentialCombinationRun2/Analyses/hig-19-016/outdir_differential_Pt

python ../../../specific_scripts/hggMultiPdf.py --input Datacard_13TeV_differential_Pt.txt

python ../../../specific_scripts/hggMultiPdf_step2.py
``` 

this will update the ROOT files containing the pdfs for the envelope.

### HZZ

In this case, we need to rename ```OutsideAcceptance``` to ```OutsideAcceptanceHZZ``` since it is treated differently between Hgg and HZZ. This is done by running, with python3:

```
python DifferentialCombinationRun2/specific_scripts/rename_HZZ_processes.py
```

### Get More Granular Theory Predictions

This step is necessary in order to combine measurements with the binning at gen level which is not aligned. In order to do this, we first need a set of SM predictions with a more granular binning. To do this, we need to use the environment created also for the ```ttH``` predictions in TK context (based on [Thomas instructions](https://gist.github.com/threiten/2c4a10df9be5e5c5938717a3d33cf9bd#extracting-theoretical-predictions)) and, from inside ```DifferentialCombinationRun2/specific_scripts```, run 
```
python3 extractThPred.py -i /pnfs/psi.ch/cms/trivcat/store/user/gallim/DifferentialCombinationHugeSamples/dev_differential_fPA_SFsysT_signal_IA_18 -c /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/specific_scripts/splitConfig_Pt_2018_MoreGranular.yml -o /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/TheoreticalPredictions/production_modes/ggH_MoreGranular/theoryPred_Pt_2018_ggHMoreGranular.all --applyNNLOPSweights --dumpTheoryUnc --inpathOA /pnfs/psi.ch/cms/trivcat/store/user/gallim/DifferentialCombinationHugeSamples/dev_differential_fPA_SFsysT_signal_OA_18 --totalXS --proc GluGluHToGG
```
which will dump the necessary files in ```DifferentialCombinationRun2/TheoreticalPredictions/production_modes/ggH_MoreGranular```.
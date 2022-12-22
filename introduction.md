# 1 - Introduction


## Repositories

In order to (re)produce the analysis, the following repositories are necessary:

- [main repo](https://gitlab.cern.ch/magalli/differentialcombinationrun2-2)
- [plotting package](https://github.com/maxgalli/DifferentialCombinationPostProcess)
- [EFTModelsStudies](https://github.com/maxgalli/EFTModelsStudies)
- [EFTScalingEquations](https://github.com/maxgalli/EFTScalingEquations/tree/differentials_220506)
- [EFT2Obs](https://github.com/maxgalli/EFT2Obs/tree/Run2Legacy_WithOtherChannels2)
- Combine + CombineHarvester
- an installation of [TK's framework](https://github.com/tklijnsma/differentialCombination2017) would be useful (even if maybe not necessary)

Other useful links:

- [EFT gist](https://gist.github.com/maxgalli/7407c634d7d5fa2ab5043ee0e434ba7c) with instrctions and links regarding EFT2Obs and MG

## Analyses

Following are the analyses that are in the combination (or have been at some point) with the link to the repos:

- $H \rightarrow \gamma \gamma$: [HIG-19-016](https://gitlab.cern.ch/magalli/hig-19-016/-/tree/for_combination)
- $H \rightarrow ZZ$: [HIG-21-009](https://gitlab.cern.ch/magalli/hig-21-009/-/tree/Run2Combination)
- $H \rightarrow WW$: [HIG-19-002](https://gitlab.cern.ch/magalli/hig-19-002/-/tree/Run2Combination)
- $H \rightarrow \tau \tau$: [HIG-20-015](https://gitlab.cern.ch/magalli/hig-20-015/-/tree/Run2Combination_renamed_correctnames)
- $H \rightarrow b b$: [HIG-19-003](https://gitlab.cern.ch/magalli/hig-19-003)
- $H \rightarrow b b$ (VBF): [HIG-21-020](https://gitlab.cern.ch/magalli/hig-21-020/-/tree/DifferentialsRun2)
- $H \rightarrow \tau \tau$ (boosted): [HIG-21-017](https://gitlab.cern.ch/magalli/hig-21-017/-/tree/DifferentialsRun2)

## Preliminary Operations

Here we summarize the prelimiary operations that have to be performed in the submodules of DifferentialCombinationRun2. In principle, all the outputs and results of these operations should already be included in the repo, but with report them here in case there are problems and they have to be repeated.

### Hgg-Envelope
The main problem that emerged with Hgg, especially during the SMEFT interpretation, is the amount of time that the envelope takes to run. It was thus decided to implement a suggestion by Andrea that consists in throwing away the pdfs that are not reasonable within the envelope. This is performed in the following way:

```
cd DifferentialCombinationRun2/Analyses/hig-19-016/outdir_differential_Pt

python ../../../specific_scripts/hggMultiPdf.py --input Datacard_13TeV_differential_Pt.txt

python ../../../specific_scripts/hggMultiPdf_step2.py
``` 

this will update the ROOT files containing the pdfs for the envelope.

### HZZ

In this case, we need to rename ```OutsideAcceptance``` to ```OutsideAcceptanceHZZ``` since it is treated differently between Hgg and HZZ.

```
python DifferentialCombinationRun2/specific_scripts/rename_HZZ_processes.py
```

### HWW

Here PGL broke every possible convention. Looks like two scripts have to be run to get proper datacards and ROOT files:

```
python DifferentialCombinationRun2/specific_scripts/rename_HWW_histos.py
cd DifferentialCombinationRun2
python3 specific_scripts/rename_HWW.py
```

after that, the changes were meaningfully committed and pushed to [this branch](https://gitlab.cern.ch/magalli/hig-19-002/-/tree/Run2Combination).

### Htt

In this case, [my branch](https://gitlab.cern.ch/magalli/hig-20-015/-/tree/Run2Combination_renamed_correctnames) mostly takes care of adding large files with git lfs and again changing the processes called ```OutsideAcceptance```.
```
python DifferentialCombinationRun2/specific_scripts/rename_Htt_histos.py
```

### HbbVBF
Two main problems have to be tackled here:
- a bug in Combine that makes it forget to append the path for extArgs systematics area; due to this, it works when you run ```text2workspace.py``` from the same directory where the ROOT files are stored, but not from anywhere else
- conventions are completely fucked (since they didn;t even know that they would have been part of a combination); this would be fine for signal strength measurements only but not for TK scenario; the processes called simply ```ggF``` have to be changed to the required ```ggH_450_500``` etc.

Both problems are solved running
```
python DifferentialCombinationRun2/specific_scripts/rename_HbbVBF_processes.py
```
a new datacard pointing to new ROOT files is created, called ```model_combined_withpaths.txt```.

We then need to prepend "hbb" to the bins, in order to make it usable also individually within SMEFT interpretation.

```
combineCards.py hbbvbf=DifferentialCombinationRun2/Analyses/hig-21-020/testModel/model_combined_withpaths.txt > DifferentialCombinationRun2/Analyses/hig-21-020/testModel/model_combined_forComb.txt
```

This is committed in [DifferentialsRun2 branch](https://gitlab.cern.ch/magalli/hig-21-020/-/tree/DifferentialsRun2).

Note that as it is now (10.11.2022), we don't have to worry about adding it to TK interpretation since the predicitons is in bins of 10 instead of 5.

### HttBoost
Again the problem of ```OutsideAcceptance```. Run:
```
python3 DifferentialCombinationRun2/specific_scripts/rename_HttBoost_histos.py
```

An important note: inside the datacard, the mass is parametrized and needs to be 125. When combining (see next chapter) we hardcode it to 125. Note that if we do this on the individual card and then use it as an input to ```combineCards.py``` this won't work becasue when it is not parametrized that fucking shit of Combine adds the prefix, so the path will be a weird non existing thing.
To hardcode 125 in the proper cards run:
```
python DifferentialCombinationRun2/specific_scripts/rename_HttBoost_related_cards.py
```

### Get More Granular Theory Predictions

This step is necessary in order to combine measurements with the binning at gen level which is not aligned. In order to do this, we first need a set of SM predictions with a more granular binning. To do this, we need to use the environment created also for the ```ttH``` predictions in TK context (based on [Thomas instructions](https://gist.github.com/threiten/2c4a10df9be5e5c5938717a3d33cf9bd#extracting-theoretical-predictions)) and, from inside ```DifferentialCombinationRun2/specific_scripts```, run 
```
python3 extractThPred.py -i /pnfs/psi.ch/cms/trivcat/store/user/gallim/DifferentialCombinationHugeSamples/dev_differential_fPA_SFsysT_signal_IA_18 -c /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/specific_scripts/splitConfig_Pt_2018_MoreGranular.yml -o /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/TheoreticalPredictions/production_modes/ggH_MoreGranular/theoryPred_Pt_2018_ggHMoreGranular.all --applyNNLOPSweights --dumpTheoryUnc --inpathOA /pnfs/psi.ch/cms/trivcat/store/user/gallim/DifferentialCombinationHugeSamples/dev_differential_fPA_SFsysT_signal_OA_18 --totalXS --proc GluGluHToGG
```
which will dump the necessary files in ```DifferentialCombinationRun2/TheoreticalPredictions/production_modes/ggH_MoreGranular```.

### Get mapping strings

Inside ```mappings.py``` there is a dictionary of lists containing the mapping strings for each tuple observable/category. The ones for the combination, given the fact that very few people gave enough of a fuck to respect the conventions, are a complete mess. What is important to remember is how they are generated.

The script used to print these pieces of crap is ```print_mappings.py```, called with ```python3``` from inside ```DifferentialCombinationRun2/specific_scripts```. Note that the last edge of each category was changed to 1200 after I received ```HbbVBF``` in order to map properly with the predictions that I did which were generated up to 1200. This will probably change in the future.
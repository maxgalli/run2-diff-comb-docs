# 3 - Signal Strength Fits

## Combine Datacards

## Convert Datacards to ROOT Workspaces

Here we call ```text2workspace.py``` for each datacard that we need, either provided from the individual analyses (in this case, we will use the ones inside ```Analyses```) or combined (```CombinedCards```).

The script provided for this is called ```produce_workspace.py```. Here is a typical call, with all paths relative to a directory ```DifferentialCombination_home```:

```
produce_workspace.py --datacard DifferentialCombinationRun2/Analyses/hig-19-002/ptH/fullmodel.txt --observable smH_PTH --category HWW --combine-metadata-dir DifferentialCombinationRun2/metadata/xs_scans --mapping DifferentialCombinationRun2/metadata/mappings/smH_PTH/HWW.yml
```

The script performs three main operations:

- dump the ROOT file containing the workspace in \${output-dir}/\${observable}/${category}.root; the ```output-dir``` flags defaults to DifferentialCombinationRun2/CombinedWorkspaces
- if the YAML file provided with ```--mapping``` exists, uses it to feed ```text2workspace```; if it does not, POIs are inferred from the datacard and a dictionary is dumped to the path provided to ```--mapping``` (this is done because the set of POIs is used also for the plotting and it is thus convenient to have it easily configurable); it is always advisable to provide a file instead of having it inferred from the datacard, given that we will change the POIs multiple times
- ~~if the argument ```--combine-metadata-dir``` is provided, another YAML file is produced at \${combine-metadata-dir}/\${observable}/${category}.yml containing the metadata to be fed to ```submit_scans.py``` (see later)~~

## Submit Scans

Here we perform scans in different ranges for the POIs, so they can later be used to build the NLLs.

For each category, the procedure consists in running a global fit (```combine``` + arguments, executed locally) and individual fits for each POI, which are submitted on the grid using ```combineTool.py```.

The script ```submit_scans.py``` takes care of performing both operations. A typical call looks like:

```
submit_scans.py --variable smH_PTH --category Htt --metadata-dir DifferentialCombinationRun2/metadata/xs_scans/smH_PTH --input-dir DifferentialCombinationRun2/CombinedWorkspaces/smH_PTH --output-dir outputs
```

It is worth mentioning the following about the arguments:

- ```--metadata-dir``` is the directory where the YAML files produced in the previous step are stored; they contain all the flags fed to the ```combine``` and ```combineTool.py``` commands
- ```--input-dir``` is where the ROOT files containing the workspaces are stored
- ```--output-dir``` is where the output files are stored: if not present, the script will create in it subdirectories like ```${output-dir}/${variable}/${category}```; if a subdirectory named ```${category}``` is already present, another called ```${category}-1, 2```, etc. will be created

## Most updated wrapper commands per observable per category (to be regularly updated)

### pT

*Hgg*

```
# produce workspace
produce_workspace.py --datacard DifferentialCombinationRun2/Analyses/hig-19-016/outdir_differential_Pt/Datacard_13TeV_differential_Pt.txt --model SM --observable smH_PTH --category Hgg

# subumit scans
submit_scans.py --model SM --observable smH_PTH --category Hgg  --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTH --output-dir outputs/SM_scans
submit_scans.py --model SM --observable smH_PTH --category Hgg_statonly --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTH --output-dir outputs/SM_scans --global-fit-file /work/gallim/DifferentialCombination_home/outputs/SM_scans/smH_PTH/Hgg/higgsCombine_POSTFIT_Hgg.MultiDimFit.mH125.38.root # note that here we use the global-fit file from before

# plot
plot_xs_scans.py --observable smH_PTH --input-dir outputs/SM_scans/smH_PTH --metadata-dir DifferentialCombinationRun2/metadata/xs_POIs/SM --output-dir outputs/SM_plots --categories Hgg
```

*HZZ*

```
# produce workspace
produce_workspace.py --datacard DifferentialCombinationRun2/Analyses/hig-21-009/pT4l/hzz4l_all_13TeV_xs_pT4l_bin_v3.txt --model SM --observable smH_PTH --category HZZ

# submit scans (only locally)
submit_scans.py --model SM --observable smH_PTH --category HZZ --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTH --output-dir DifferentialCombinationRun2/outputs/SM_scans

# plot
plot_xs_scans.py --observable smH_PTH --input-dir DifferentialCombinationRun2/outputs/SM_scans/smH_PTH --metadata-dir DifferentialCombinationRun2/metadata/xs_POIs/SM --output-dir outputs/SM_plots --categories HZZ
```

*HWW*

```
# produce workspace
produce_workspace.py --datacard DifferentialCombinationRun2/Analyses/hig-19-002/ptH_for_differential_combination/fullmodel_unreg.txt --model SM --observable smH_PTH --category HWW

# submit scans
# last POI might have to be done in a separate step because the range to be scanned is wider than the others
submit_scans.py --model SM --observable smH_PTH --category HWW --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTH --output-dir outputs/SM_scans

submit_scans.py --model SM --observable smH_PTH --category HWW_statonly --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTH --output-dir outputs/SM_scans --global-fit-file /work/gallim/DifferentialCombination_home/outputs/SM_scans/smH_PTH/HWW/higgsCombine_POSTFIT_HWW.MultiDimFit.mH125.38.root

submit_scans.py --model SM --observable smH_PTH --category HWW_asimov --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTH --output-dir outputs/SM_scans
```

*Htt*

```
# produce workspace
produce_workspace.py --datacard DifferentialCombinationRun2/Analyses/hig-20-015/HiggsPt/HTT_Run2FinalCard_HiggsPt_NoReg.txt --model SM --observable smH_PTH --category Htt

# submit scans
submit_scans.py --model SM --observable smH_PTH --category Htt  --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTH --output-dir outputs/SM_scans
```

*HbbVBF*

```
# first of all, we need to add the absolute path to the part that refers to HbbVBF
# this is due to the fact that apparently if a nuisance is referred to as extArgs in the card, the path where we are calling text2workspace.py from is not appended, as it happens in every other case
python DifferentialCombinationRun2/specific_scripts/add_prefix_to_HbbVBF.py

# produce workspace
produce_workspace.py --datacard DifferentialCombinationRun2/Analyses/hig-21-020/testModel/model_combined_withpaths.txt --model SM --observable smH_PTH --category HbbVBF

# submit scans
submit_scans.py --model SM --observable smH_PTH --category HbbVBF_asimov  --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTH --output-dir outputs/SM_scans
submit_scans.py --model SM --observable smH_PTH --category HbbVBF  --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTH --output-dir outputs/SM_scans

# plot
plot_xs_scans.py --observable smH_PTH --input-dir outputs/SM_scans/smH_PTH --metadata-dir DifferentialCombinationRun2/metadata/xs_POIs/SM --output-dir outputs/SM_plots --singles HbbVBF
```

*HttBoost*

```
# produce workspace
produce_workspace.py --datacard DifferentialCombinationRun2/Analyses/hig-21-017/BoostedHTT_DiffXS_HiggsPt_NoOverLap/V2_Diff_dr0p5_hpt_2bin/hig-21-017_hpt.txt --model SM --observable smH_PTH --category HttBoost

# submit scans
submit_scans.py --model SM --observable smH_PTH --category HttBoost_asimov  --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTH --output-dir outputs/SM_scans
submit_scans.py --model SM --observable smH_PTH --category HttBoost  --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTH --output-dir outputs/SM_scans

# plot
plot_xs_scans.py --observable smH_PTH --input-dir outputs/SM_scans/smH_PTH --metadata-dir DifferentialCombinationRun2/metadata/xs_POIs/SM --output-dir outputs/SM_plots --categories HttBoost
```

*HggHZZ*

```
# note that combining the symlink won't work due to stupid shit done inside Combine, with the paths hardcoded in the cards
combineCards.py DifferentialCombinationRun2/Analyses/hig-19-016/outdir_differential_Pt/Datacard_13TeV_differential_Pt.txt DifferentialCombinationRun2/Analyses/hig-21-009/forCombination/smH_PTH_HZZ_processesRenumbered.txt > DifferentialCombinationRun2/CombinedCards/smH_PTH/HggHZZ.txt 

# produce workspace
produce_workspace.py --datacard DifferentialCombinationRun2/CombinedCards/smH_PTH/HggHZZ.txt --model SM --observable smH_PTH --category HggHZZ

# submit scans
submit_scans.py --model SM --observable smH_PTH --category HggHZZ --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTH --output-dir outputs/SM_scans

submit_scans.py --model SM --observable smH_PTH --category HggHZZ_statonly --input-dir DifferentialCombinationRun2/CombinedWorkspaces/smH_PTH --output-dir outputs/SM_scans --global-fit-file /work/gallim/DifferentialCombination_home/outputs/SM_scans/smH_PTH/HggHZZ/higgsCombine_POSTFIT_HggHZZ.MultiDimFit.mH125.38.root
```

*HggHZZHWW*

```
# combine cards
combineCards.py hgg=DifferentialCombinationRun2/Analyses/hig-19-016/outdir_differential_Pt/Datacard_13TeV_differential_Pt.txt hzz=DifferentialCombinationRun2/Analyses/hig-21-009/forCombination/smH_PTH_HZZ_processesRenumbered.txt hww=DifferentialCombinationRun2/Analyses/hig-19-002/ptH_for_differential_combination/fullmodel_unreg.txt > DifferentialCombinationRun2/CombinedCards/smH_PTH/HggHZZHWW.txt

# produce workspace
produce_workspace.py --datacard DifferentialCombinationRun2/CombinedCards/smH_PTH/HggHZZHWW.txt --model SM --observable smH_PTH --category HggHZZHWW

# submit scans
submit_scans.py --model SM --observable smH_PTH --category HggHZZHWW --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTH --output-dir outputs/SM_scans

submit_scans.py --model SM --observable smH_PTH --category HggHZZHWW_statonly --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTH --output-dir outputs/SM_scans --global-fit-file /work/gallim/DifferentialCombination_home/outputs/SM_scans/smH_PTH/HggHZZHWW/higgsCombine_POSTFIT_HggHZZHWW.MultiDimFit.mH125.38.root

# plot
plot_xs_scans.py --observable smH_PTH --input-dir outputs/SM_scans/smH_PTH --metadata-dir DifferentialCombinationRun2/metadata/xs_POIs/SM --output-dir outputs/SM_plots --categories HggHZZHWW Hgg HZZ HWW --systematic-bands HggHZZHWW
```

Note that before plotting a few actions might need to be performed:
- it is not guaranteed that bin 0_5 will have a large enough range for the scan
- bins 80_100 and 200_250 usually show a few points in the scan that are completely fucked; either shrink the scan range or remove them manually using ```DifferentialCombinationRun2/specific_scripts/debug_scans_output.py```

*HggHZZHWWHtt*

```
# combine cards
combineCards.py hgg=DifferentialCombinationRun2/Analyses/hig-19-016/outdir_differential_Pt/Datacard_13TeV_differential_Pt.txt hzz=DifferentialCombinationRun2/Analyses/hig-21-009/forCombination/smH_PTH_HZZ_processesRenumbered.txt hww=DifferentialCombinationRun2/Analyses/hig-19-002/ptH_for_differential_combination/fullmodel_unreg.txt htt=DifferentialCombinationRun2/Analyses/hig-20-015/HiggsPt/HTT_Run2FinalCard_HiggsPt_NoReg.txt > DifferentialCombinationRun2/CombinedCards/smH_PTH/HggHZZHWWHtt.txt

# produce workspace
pproduce_workspace.py --datacard DifferentialCombinationRun2/CombinedCards/smH_PTH/HggHZZHWWHtt.txt --model SM --observable smH_PTH --category HggHZZHWWHtt

# submit scans
submit_scans.py --model SM --observable smH_PTH --category HggHZZHWWHtt  --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTH --output-dir outputs/SM_scans
submit_scans.py --model SM --observable smH_PTH --category HggHZZHWWHtt_asimov  --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTH --output-dir outputs/SM_scans

# plot
plot_xs_scans.py --observable smH_PTH --input-dir outputs/SM_scans/smH_PTH --metadata-dir DifferentialCombinationRun2/metadata/xs_POIs/SM --output-dir outputs/SM_plots --categories HggHZZHWWHtt Hgg HZZ HWW --singles Htt --systematic-bands HggHZZHWWHtt

```

*HggHZZHWWHttHbb*

```
# combine cards
combineCards.py hgg=DifferentialCombinationRun2/Analyses/hig-19-016/outdir_differential_Pt/Datacard_13TeV_differential_Pt.txt hzz=DifferentialCombinationRun2/Analyses/hig-21-009/pT4l/hzz4l_all_13TeV_xs_pT4l_bin_v3.txt hww=DifferentialCombinationRun2/Analyses/hig-19-002/ptH_for_differential_combination/fullmodel_unreg.txt htt=DifferentialCombinationRun2/Analyses/hig-20-015/HiggsPt/HTT_Run2FinalCard_HiggsPt_NoReg.txt hbb=DifferentialCombinationRun2/Analyses/hig-19-003/HJMINLO_fidxs/cms_datacard_hig-19-003-forDifferential.txt > DifferentialCombinationRun2/CombinedCards/smH_PTH/HggHZZHWWHttHbb.txt

# produce workspace
produce_workspace.py --datacard DifferentialCombinationRun2/CombinedCards/smH_PTH/HggHZZHWWHttHbb.txt --model SM --observable smH_PTH --category HggHZZHWWHttHbb

# submit scans
submit_scans.py --model SM --observable smH_PTH --category HggHZZHWWHttHbb  --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTH --output-dir outputs/SM_scans
submit_scans.py --model SM --observable smH_PTH --category HggHZZHWWHttHbb_asimov  --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTH --output-dir outputs/SM_scans

# plot
plot_xs_scans.py --observable smH_PTH --input-dir DifferentialCombinationRun2/outputs/SM_scans/smH_PTH --metadata-dir DifferentialCombinationRun2/metadata/xs_POIs/SM --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/SM_plots --categories HggHZZHWWHttHbb Hgg HZZ HWW --singles Htt Hbb --systematic-bands HggHZZHWWHttHbb --config-file DifferentialCombinationRun2/metadata/xs_POIs/SM/smH_PTH/plot_config.yml
```


*HggHZZHWWHttHbbVBF*

```
# combine cards
combineCards.py hgg=DifferentialCombinationRun2/Analyses/hig-19-016/outdir_differential_Pt/Datacard_13TeV_differential_Pt.txt hzz=DifferentialCombinationRun2/Analyses/hig-21-009/pT4l/hzz4l_all_13TeV_xs_pT4l_bin_v3.txt hww=DifferentialCombinationRun2/Analyses/hig-19-002/ptH_for_differential_combination/fullmodel_unreg.txt htt=DifferentialCombinationRun2/Analyses/hig-20-015/HiggsPt/HTT_Run2FinalCard_HiggsPt_NoReg.txt hbbvbf=DifferentialCombinationRun2/Analyses/hig-21-020/testModel/model_combined_withpaths.txt > DifferentialCombinationRun2/CombinedCards/smH_PTH/HggHZZHWWHttHbbVBF.txt

# produce workspace
produce_workspace.py --datacard DifferentialCombinationRun2/CombinedCards/smH_PTH/HggHZZHWWHttHbbVBF.txt --model SM --observable smH_PTH --category HggHZZHWWHttHbbVBF

# submit scans
submit_scans.py --model SM --observable smH_PTH --category HggHZZHWWHttHbbVBF_asimov  --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTH --output-dir outputs/SM_scans
submit_scans.py --model SM --observable smH_PTH --category HggHZZHWWHttHbbVBF  --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTH --output-dir outputs/SM_scans

# plot
plot_xs_scans.py --observable smH_PTH --input-dir outputs/SM_scans/smH_PTH --metadata-dir DifferentialCombinationRun2/metadata/xs_POIs/SM --output-dir outputs/SM_plots --categories HggHZZHWWHttHbbVBF --config-file DifferentialCombinationRun2/metadata/xs_POIs/SM/smH_PTH/plot_config.yml
```

*HggHZZHWWHttHbbVBFHttBoost*

```
# combine cards
combineCards.py hgg=DifferentialCombinationRun2/Analyses/hig-19-016/outdir_differential_Pt/Datacard_13TeV_differential_Pt.txt hzz=DifferentialCombinationRun2/Analyses/hig-21-009/pT4l/hzz4l_all_13TeV_xs_pT4l_bin_v3.txt hww=DifferentialCombinationRun2/Analyses/hig-19-002/ptH_for_differential_combination/fullmodel_unreg.txt htt=DifferentialCombinationRun2/Analyses/hig-20-015/HiggsPt/HTT_Run2FinalCard_HiggsPt_NoReg.txt hbbvbf=DifferentialCombinationRun2/Analyses/hig-21-020/testModel/model_combined_withpaths.txt httboost=DifferentialCombinationRun2/Analyses/hig-21-017/BoostedHTT_DiffXS_HiggsPt_NoOverLap/V2_Diff_dr0p5_hpt_2bin/hig-21-017_hpt.txt > DifferentialCombinationRun2/CombinedCards/smH_PTH/HggHZZHWWHttHbbVBFHttBoost.txt

# then you need to somehow change every instance of $MASS with 125
python DifferentialCombinationRun2/specific_scripts/rename_HttBoost_related_cards.py

# produce workspace
produce_workspace.py --datacard DifferentialCombinationRun2/CombinedCards/smH_PTH/HggHZZHWWHttHbbVBFHttBoost.txt --model SM --observable smH_PTH --category HggHZZHWWHttHbbVBFHttBoost

# submit scans

```

*HggHZZInclusive*

```
# produce workspace
produce_workspace.py --datacard DifferentialCombinationRun2/CombinedCards/smH_PTH/HggHZZ.txt --model SM --observable smH_PTH --category HggHZZinclusive

# submit scans
submit_scans.py --model SM --observable smH_PTH --category HggHZZinclusive --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTH --output-dir outputs/SM_scans

# plot
```

### Njets

*Hgg*

```
# Not up-to-date with new metadata 
# produce workspace
produce_workspace.py --datacard DifferentialCombinationRun2/Analyses/hig-19-016/outdir_differential_Njets2p5/Datacard_13TeV_differential_Njets2p5.txt --observable Njets --category Hgg --mapping DifferentialCombinationRun2/metadata/mappings/Njets/Hgg.yml

# submit scans
submit_scans.py --model SM --observable Njets --category Hgg --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/Njets --output-dir outputs/SM_scans

# plot
plot_xs_scans.py --observable Njets --input-dir outputs/SM_scans/Njets --metadata-dir DifferentialCombinationRun2/metadata/xs_POIs/SM --output-dir outputs/SM_plots --categories Hgg
```

*HZZ*

```
# produce workspace
produce_workspace.py --datacard DifferentialCombinationRun2/Analyses/hig-21-009/njets_pt30_eta4p7/hzz4l_all_13TeV_xs_njets_pt30_eta4p7_bin_v3.txt --model SM --observable Njets --category HZZ

# submit scans

```

*HWW*

```
# produce workspace
produce_workspace.py --datacard DifferentialCombinationRun2/Analyses/hig-19-002/njet/fullmodel.txt --model SM --observable Njets --category HWW

# submit scans
submit_scans.py --model SM --observable Njets --category HWW --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/Njets --output-dir outputs/SM_scans
```

*Htt*

```
# produce workspace
produce_workspace.py --datacard DifferentialCombinationRun2/Analyses/hig-20-015/NJets/HTT_Run2FinalCard_NJets_NoReg.txt --model SM --observable Njets --category Htt

# submit scans
submit_scans.py --model SM --observable Njets --category Htt  --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/Njets --output-dir outputs/SM_scans
```

*HggHWW*

```
# combine cards
combineCards.py hgg=DifferentialCombinationRun2/Analyses/hig-19-016/outdir_differential_Njets2p5/Datacard_13TeV_differential_Njets2p5.txt hww=DifferentialCombinationRun2/Analyses/hig-19-002/njet/fullmodel.txt > DifferentialCombinationRun2/CombinedCards/Njets/HggHWW.txt

# produce workspace
produce_workspace.py --datacard DifferentialCombinationRun2/CombinedCards/Njets/HggHWW.txt --model SM --observable Njets --category HggHWW

# submit scans
submit_scans.py --model SM --observable Njets --category HggHWW --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/Njets --output-dir outputs/SM_scans
submit_scans.py --model SM --observable Njets --category HggHWW_statonly --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/Njets --output-dir outputs/SM_scans --global-fit-file /work/gallim/DifferentialCombination_home/outputs/SM_scans/Njets/HggHWW/higgsCombine_POSTFIT_HggHWW.MultiDimFit.mH125.38.root

# plot
plot_xs_scans.py --observable Njets --input-dir outputs/SM_scans/Njets --metadata-dir DifferentialCombinationRun2/metadata/xs_POIs/SM --output-dir outputs/SM_plots --categories HggHWW Hgg HWW --systematic-bands HggHWW
```

*HggHWWHtt*

```
# combine cards
combineCards.py hgg=DifferentialCombinationRun2/Analyses/hig-19-016/outdir_differential_Njets2p5/Datacard_13TeV_differential_Njets2p5.txt hww=DifferentialCombinationRun2/Analyses/hig-19-002/njet/fullmodel.txt htt=DifferentialCombinationRun2/Analyses/hig-20-015/NJets/HTT_Run2FinalCard_NJets_NoReg.txt > DifferentialCombinationRun2/CombinedCards/Njets/HggHWWHtt.txt

# produce workspace
produce_workspace.py --datacard DifferentialCombinationRun2/CombinedCards/Njets/HggHWWHtt.txt --model SM --observable Njets --category HggHWWHtt
```

*HggHZZHWWHtt*

```
# combine cards
combineCards.py hgg=DifferentialCombinationRun2/Analyses/hig-19-016/outdir_differential_Njets2p5/Datacard_13TeV_differential_Njets2p5.txt hzz=DifferentialCombinationRun2/Analyses/hig-21-009/njets_pt30_eta4p7/hzz4l_all_13TeV_xs_njets_pt30_eta4p7_bin_v3.txt hww=DifferentialCombinationRun2/Analyses/hig-19-002/njet/fullmodel.txt htt=DifferentialCombinationRun2/Analyses/hig-20-015/NJets/HTT_Run2FinalCard_NJets_NoReg.txt > DifferentialCombinationRun2/CombinedCards/Njets/HggHZZHWWHtt.txt

# produce workspace

# submit scans
submit_scans.py --model SM --observable Njets --category HggHZZHWWHtt --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/Njets --output-dir outputs/SM_scans
```

### yH

*Hgg*

```
# produce workspace
produce_workspace.py --datacard DifferentialCombinationRun2/Analyses/hig-19-016/outdir_differential_AbsRapidityFine/Datacard_13TeV_differential_AbsRapidityFine.txt --model SM --observable yH --category Hgg

# submit scans
submit_scans.py --model SM --observable yH --category Hgg  --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/yH --output-dir outputs/SM_scans
```

*HZZ*

```
# produce workspace
produce_workspace.py --datacard DifferentialCombinationRun2/Analyses/hig-21-009/rapidity4l/hzz4l_all_13TeV_xs_rapidity4l_bin_v3.txt --model SM --observable yH --category HZZ

# submit scans (only locally)
submit_scans.py --model SM --observable yH --category HZZ --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/yH --output-dir outputs/SM_scans

submit_scans.py --model SM --observable yH --category HZZ_statonly --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/yH --output-dir outputs/SM_scans

submit_scans.py --model SM --observable yH --category HZZ_asimov --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/yH --output-dir outputs/SM_scans

# plot
plot_xs_scans.py --observable yH --input-dir outputs/SM_scans/yH --metadata-dir DifferentialCombinationRun2/metadata/xs_POIs/SM --output-dir outputs/SM_plots --categories HZZ
```

*HggHZZ*

```
# combine cards
combineCards.py hgg=DifferentialCombinationRun2/Analyses/hig-19-016/outdir_differential_AbsRapidityFine/Datacard_13TeV_differential_AbsRapidityFine.txt hzz=DifferentialCombinationRun2/Analyses/hig-21-009/rapidity4l/hzz4l_all_13TeV_xs_rapidity4l_bin_v3.txt > DifferentialCombinationRun2/CombinedCards/yH/HggHZZ.txt

# produce workspace
produce_workspace.py --datacard DifferentialCombinationRun2/CombinedCards/yH/HggHZZ.txt --model SM --observable yH --category HggHZZ

# submit scans
submit_scans.py --model SM --observable yH --category HggHZZ --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/yH --output-dir outputs/SM_scans

submit_scans.py --model SM --observable yH --category HggHZZ_statonly --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/yH --output-dir outputs/SM_scans --global-fit-file /work/gallim/DifferentialCombination_home/outputs/SM_scans/yH/HggHZZ/higgsCombine_POSTFIT_HggHZZ.MultiDimFit.mH125.38.root

# plot
plot_xs_scans.py --observable yH --input-dir outputs/SM_scans/yH --metadata-dir DifferentialCombinationRun2/metadata/xs_POIs/SM --output-dir outputs/SM_plots --categories HggHZZ Hgg HZZ --systematic-bands HggHZZ
```

*HggInclusive*

```
produce_workspace.py --datacard DifferentialCombinationRun2/Analyses/hig-19-016/outdir_differential_AbsRapidityFine/Datacard_13TeV_differential_AbsRapidityFine.txt --model SM --observable yH --category Hgginclusive

submit_scans.py --model SM --observable yH --category Hgginclusive --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/yH --output-dir outputs/SM_scans
```

*HZZInclusive*

```
submit_scans.py --model SM --observable yH --category HggHZZinclusive_asimov --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/yH --output-dir outputs/SM_scans

submit_scans.py --model SM --observable yH --category HZZinclusive --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/yH --output-dir outputs/SM_scans
```

*HggHZZInclusive*

```
# produce workspace
produce_workspace.py --datacard DifferentialCombinationRun2/CombinedCards/yH/HggHZZ.txt --model SM --observable yH --category HggHZZinclusive

# submit scans
submit_scans.py --model SM --observable yH --category HggHZZinclusive --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/yH --output-dir outputs/SM_scans
submit_scans.py --model SM --observable yH --category HggHZZinclusive_statonly --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/yH --output-dir outputs/SM_scans --global-fit-file /work/gallim/DifferentialCombination_home/outputs/SM_scans/yH/HggHZZinclusive/higgsCombine_POSTFIT_HggHZZinclusive.MultiDimFit.mH125.38.root

# plot
plot_xs_scans.py --observable yH --input-dir outputs/SM_scans/yH --metadata-dir DifferentialCombinationRun2/metadata/xs_POIs/SM --output-dir outputs/SM_plots --categories HggHZZinclusive --no-final
```

### pT J0

*Hgg*

```
# produce workspace
produce_workspace.py --datacard DifferentialCombinationRun2/Analyses/hig-19-016/outdir_differential_Jet2p5Pt0/Datacard_13TeV_differential_Jet2p5Pt0.txt --model SM --observable smH_PTJ0 --category Hgg

# submit scans
submit_scans.py --model SM --observable smH_PTJ0 --category Hgg_asimov  --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTJ0 --output-dir DifferentialCombinationRun2/outputs/SM_scans
submit_scans.py --model SM --observable smH_PTJ0 --category Hgg_asimov_statonly  --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTJ0 --output-dir DifferentialCombinationRun2/outputs/SM_scans --global-fit-file $PWD/DifferentialCombinationRun2/outputs/SM_scans/smH_PTJ0/Hgg_asimov-20220919xxx111846/higgsCombineAsimovPostFit.GenerateOnly.mH125.38.123456.root
submit_scans.py --model SM --observable smH_PTJ0 --category Hgg  --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTJ0 --output-dir DifferentialCombinationRun2/outputs/SM_scans
```


## Specific instructions per category (Hgg, Hzz, Hgg_Hzz, etc.) - not sure if this is ever going to be useful

Ahahahahah porcodiqueldio ma come si fa a basare un'analisi su una mera serie di incomprensibili linee di comando chilometriche?!?!?

### Hgg

*pT*

```
cd /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/Analyses/hig-19-016


# make workspace
text2workspace.py -P HiggsAnalysis.CombinedLimit.PhysicsModel:multiSignalModel --PO verbose -m 125.38 --PO 'higgsMassRange=123,127' --PO 'map=.*/smH_PTH_0p0_5p0:r_smH_PTH_0_5[1,-1,3]' --PO 'map=.*/smH_PTH_5p0_10p0:r_smH_PTH_5_10[1,-1,3]' --PO 'map=.*/smH_PTH_10p0_15p0:r_smH_PTH_10_15[1,-1,3]' --PO 'map=.*/smH_PTH_15p0_20p0:r_smH_PTH_15_20[1,-1,3]' --PO 'map=.*/smH_PTH_20p0_25p0:r_smH_PTH_20_25[1,-1,3]' --PO 'map=.*/smH_PTH_25p0_30p0:r_smH_PTH_25_30[1,-1,3]' --PO 'map=.*/smH_PTH_30p0_35p0:r_smH_PTH_30_35[1,-1,3]' --PO 'map=.*/smH_PTH_35p0_45p0:r_smH_PTH_35_45[1,-1,3]' --PO 'map=.*/smH_PTH_45p0_60p0:r_smH_PTH_45_60[1,-1,3]' --PO 'map=.*/smH_PTH_60p0_80p0:r_smH_PTH_60_80[1,-1,3]' --PO 'map=.*/smH_PTH_80p0_100p0:r_smH_PTH_80_100[1,-1,3]' --PO 'map=.*/smH_PTH_100p0_120p0:r_smH_PTH_100_120[1,-1,3]' --PO 'map=.*/smH_PTH_120p0_140p0:r_smH_PTH_120_140[1,-1,3]' --PO 'map=.*/smH_PTH_140p0_170p0:r_smH_PTH_140_170[1,-1,3]' --PO 'map=.*/smH_PTH_170p0_200p0:r_smH_PTH_170_200[1,-1,3]' --PO 'map=.*/smH_PTH_200p0_250p0:r_smH_PTH_200_250[1,-1,3]' --PO 'map=.*/smH_PTH_250p0_350p0:r_smH_PTH_250_350[1,-1,3]' --PO 'map=.*/smH_PTH_350p0_450p0:r_smH_PTH_350_450[1,-1,3]' --PO 'map=.*/smH_PTH_450p0_10000p0:r_smH_PTH_GT450[1,-1,3]' -o /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/CombinedWorkspaces/smH_PTH/Hgg.root outdir_differential_Pt/Datacard_13TeV_differential_Pt.txt

cd /work/gallim/DifferentialCombination_home/outputs/smH_PTH/Hgg

# fit data
combine /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTH/Hgg.root -M MultiDimFit -m 125.38 --saveWorkspace --name _POSTFIT_Hgg --setParameters r_smH_PTH_10_15=1,r_smH_PTH_120_140=1,r_smH_PTH_25_30=1,r_smH_PTH_60_80=1,r_smH_PTH_200_250=1,r_smH_PTH_80_100=1,r_smH_PTH_45_60=1,r_smH_PTH_30_35=1,r_smH_PTH_350_450=1,r_smH_PTH_5_10=1,r_smH_PTH_140_170=1,r_smH_PTH_170_200=1,r_smH_PTH_100_120=1,r_smH_PTH_250_350=1,r_smH_PTH_0_5=1,r_smH_PTH_GT450=1,r_smH_PTH_15_20=1,r_smH_PTH_20_25=1,r_smH_PTH_35_45=1 --redefineSignalPOIs r_smH_PTH_15_20,r_smH_PTH_45_60,r_smH_PTH_170_200,r_smH_PTH_120_140,r_smH_PTH_80_100,r_smH_PTH_200_250,r_smH_PTH_GT450,r_smH_PTH_35_45,r_smH_PTH_350_450,r_smH_PTH_60_80,r_smH_PTH_250_350,r_smH_PTH_20_25,r_smH_PTH_140_170,r_smH_PTH_30_35,r_smH_PTH_0_5,r_smH_PTH_25_30,r_smH_PTH_5_10,r_smH_PTH_100_120,r_smH_PTH_10_15 --X-rtd MINIMIZER_freezeDisassociatedParams --cminDefaultMinimizerStrategy 0

for poi in r_smH_PTH_15_20 r_smH_PTH_45_60 r_smH_PTH_170_200 r_smH_PTH_120_140 r_smH_PTH_80_100 r_smH_PTH_200_250 r_smH_PTH_GT450 r_smH_PTH_35_45 r_smH_PTH_350_450 r_smH_PTH_60_80 r_smH_PTH_250_350 r_smH_PTH_20_25 r_smH_PTH_140_170 r_smH_PTH_30_35 r_smH_PTH_0_5 r_smH_PTH_25_30 r_smH_PTH_5_10 r_smH_PTH_100_120 r_smH_PTH_10_15; do combineTool.py higgsCombine_POSTFIT_Hgg.MultiDimFit.mH125.38.root -M MultiDimFit -m 125.38 --snapshotName "MultiDimFit" -v -1 -P ${poi} --floatOtherPOIs 1 --name _SCAN_${poi}_Hgg --redefineSignalPOIs r_smH_PTH_15_20,r_smH_PTH_45_60,r_smH_PTH_170_200,r_smH_PTH_120_140,r_smH_PTH_80_100,r_smH_PTH_200_250,r_smH_PTH_GT450,r_smH_PTH_35_45,r_smH_PTH_350_450,r_smH_PTH_60_80,r_smH_PTH_250_350,r_smH_PTH_20_25,r_smH_PTH_140_170,r_smH_PTH_30_35,r_smH_PTH_0_5,r_smH_PTH_25_30,r_smH_PTH_5_10,r_smH_PTH_100_120,r_smH_PTH_10_15 --X-rtd MINIMIZER_freezeDisassociatedParams --cminDefaultMinimizerStrategy 0 --algo grid --split-points 1 --points 30 --squareDistPoiStep --sub-opts="--mem=5G" --job-mode slurm --task-name _SCAN_${poi}_Hgg; done

# fit asimov
cd /work/gallim/DifferentialCombination_home/outputs/smH_PTH/Hgg_asimov

combine /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTH/Hgg.root -M GenerateOnly -m 125.38 --setParameters r_smH_PTH_0_5=1,r_smH_PTH_5_10=1,r_smH_PTH_10_15=1,r_smH_PTH_15_20=1,r_smH_PTH_20_25=1,r_smH_PTH_25_30=1,r_smH_PTH_30_35=1,r_smH_PTH_35_45=1,r_smH_PTH_45_60=1,r_smH_PTH_60_80=1,r_smH_PTH_80_100=1,r_smH_PTH_100_120=1,r_smH_PTH_120_140=1,r_smH_PTH_140_170=1,r_smH_PTH_170_200=1,r_smH_PTH_200_250=1,r_smH_PTH_250_350=1,r_smH_PTH_350_450=1,r_smH_PTH_GT450=1 -n AsimovPreFit --saveToys -t -1

combine /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/CombinedWorkspaces/SM/smH_PTH/Hgg.root -t -1 --toysFile higgsCombineAsimovPreFit.GenerateOnly.mH125.38.123456.root -M MultiDimFit -m 125.38 --saveWorkspace -n AsimovBestFit --X-rtd MINIMIZER_freezeDisassociatedParams --cminDefaultMinimizerStrategy 0 --setParameters r_smH_PTH_10_15=1,r_smH_PTH_120_140=1,r_smH_PTH_25_30=1,r_smH_PTH_60_80=1,r_smH_PTH_200_250=1,r_smH_PTH_80_100=1,r_smH_PTH_45_60=1,r_smH_PTH_30_35=1,r_smH_PTH_350_450=1,r_smH_PTH_5_10=1,r_smH_PTH_140_170=1,r_smH_PTH_170_200=1,r_smH_PTH_100_120=1,r_smH_PTH_250_350=1,r_smH_PTH_0_5=1,r_smH_PTH_GT450=1,r_smH_PTH_15_20=1,r_smH_PTH_20_25=1,r_smH_PTH_35_45=1 --redefineSignalPOIs r_smH_PTH_15_20,r_smH_PTH_45_60,r_smH_PTH_170_200,r_smH_PTH_120_140,r_smH_PTH_80_100,r_smH_PTH_200_250,r_smH_PTH_GT450,r_smH_PTH_35_45,r_smH_PTH_350_450,r_smH_PTH_60_80,r_smH_PTH_250_350,r_smH_PTH_20_25,r_smH_PTH_140_170,r_smH_PTH_30_35,r_smH_PTH_0_5,r_smH_PTH_25_30,r_smH_PTH_5_10,r_smH_PTH_100_120,r_smH_PTH_10_15

combine higgsCombineAsimovBestFit.MultiDimFit.mH125.38.root -M GenerateOnly -m 125.38 --setParameters r_smH_PTH_10_15=1,r_smH_PTH_120_140=1,r_smH_PTH_25_30=1,r_smH_PTH_60_80=1,r_smH_PTH_200_250=1,r_smH_PTH_80_100=1,r_smH_PTH_45_60=1,r_smH_PTH_30_35=1,r_smH_PTH_350_450=1,r_smH_PTH_5_10=1,r_smH_PTH_140_170=1,r_smH_PTH_170_200=1,r_smH_PTH_100_120=1,r_smH_PTH_250_350=1,r_smH_PTH_0_5=1,r_smH_PTH_GT450=1,r_smH_PTH_15_20=1,r_smH_PTH_20_25=1,r_smH_PTH_35_45=1 -n AsimovPostFit --saveToys --saveWorkspace --snapshotName MultiDimFit -t -1

for poi in r_smH_PTH_15_20 r_smH_PTH_45_60 r_smH_PTH_170_200 r_smH_PTH_120_140 r_smH_PTH_80_100 r_smH_PTH_200_250 r_smH_PTH_GT450 r_smH_PTH_35_45 r_smH_PTH_350_450 r_smH_PTH_60_80 r_smH_PTH_250_350 r_smH_PTH_20_25 r_smH_PTH_140_170 r_smH_PTH_30_35 r_smH_PTH_0_5 r_smH_PTH_25_30 r_smH_PTH_5_10 r_smH_PTH_100_120 r_smH_PTH_10_15; do combineTool.py higgsCombineAsimovPostFit.GenerateOnly.mH125.38.123456.root -M MultiDimFit -m 125.38 --snapshotName "MultiDimFit" -t -1 -v -1 -P ${poi} --floatOtherPOIs 1 --name _SCAN_${poi}_Hgg --redefineSignalPOIs r_smH_PTH_15_20,r_smH_PTH_45_60,r_smH_PTH_170_200,r_smH_PTH_120_140,r_smH_PTH_80_100,r_smH_PTH_200_250,r_smH_PTH_GT450,r_smH_PTH_35_45,r_smH_PTH_350_450,r_smH_PTH_60_80,r_smH_PTH_250_350,r_smH_PTH_20_25,r_smH_PTH_140_170,r_smH_PTH_30_35,r_smH_PTH_0_5,r_smH_PTH_25_30,r_smH_PTH_5_10,r_smH_PTH_100_120,r_smH_PTH_10_15 --X-rtd MINIMIZER_freezeDisassociatedParams --cminDefaultMinimizerStrategy 0 --squareDistPoiStep --toysFile higgsCombineAsimovPostFit.GenerateOnly.mH125.38.123456.root --algo grid --split-points 1 --points 30 --sub-opts="--mem=5G --partition=long --time=20:00:00" --job-mode slurm --task-name _SCAN_${poi}_Hgg_asimov; done

cd ../Hgg_asimov_statonly
cp ../Hgg_asimov/higgsCombineAsimovPostFit.GenerateOnly.mH125.38.123456.root .

for poi in r_smH_PTH_15_20 r_smH_PTH_45_60 r_smH_PTH_170_200 r_smH_PTH_120_140 r_smH_PTH_80_100 r_smH_PTH_200_250 r_smH_PTH_GT450 r_smH_PTH_35_45 r_smH_PTH_350_450 r_smH_PTH_60_80 r_smH_PTH_250_350 r_smH_PTH_20_25 r_smH_PTH_140_170 r_smH_PTH_30_35 r_smH_PTH_0_5 r_smH_PTH_25_30 r_smH_PTH_5_10 r_smH_PTH_100_120 r_smH_PTH_10_15; do combineTool.py higgsCombineAsimovPostFit.GenerateOnly.mH125.38.123456.root -M MultiDimFit -m 125.38 --snapshotName "MultiDimFit" -t -1 -v -1 -P ${poi} --floatOtherPOIs 1 --name _SCAN_${poi}_Hgg --redefineSignalPOIs r_smH_PTH_15_20,r_smH_PTH_45_60,r_smH_PTH_170_200,r_smH_PTH_120_140,r_smH_PTH_80_100,r_smH_PTH_200_250,r_smH_PTH_GT450,r_smH_PTH_35_45,r_smH_PTH_350_450,r_smH_PTH_60_80,r_smH_PTH_250_350,r_smH_PTH_20_25,r_smH_PTH_140_170,r_smH_PTH_30_35,r_smH_PTH_0_5,r_smH_PTH_25_30,r_smH_PTH_5_10,r_smH_PTH_100_120,r_smH_PTH_10_15 --X-rtd MINIMIZER_freezeDisassociatedParams --cminDefaultMinimizerStrategy 0 --squareDistPoiStep --toysFile higgsCombineAsimovPostFit.GenerateOnly.mH125.38.123456.root --algo grid --freezeParameters allConstrainedNuisances --split-points 1 --points 30 --sub-opts="--mem=5G --partition=long --time=20:00:00" --job-mode slurm --task-name _SCAN_${poi}_Hgg_asimov; done
```

### HZZ

*pT*

```
cd /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/Analyses/hig-21-009/forCombination

combineCards.py hzz_PTH_0p0_15p0_cat4e_2016=datacard_2016/hzz4l_4eS_13TeV_xs_pT4l_bin0_v3.txt hzz_PTH_15p0_30p0_cat4e_2016=datacard_2016/hzz4l_4eS_13TeV_xs_pT4l_bin1_v3.txt hzz_PTH_30p0_45p0_cat4e_2016=datacard_2016/hzz4l_4eS_13TeV_xs_pT4l_bin2_v3.txt hzz_PTH_45p0_80p0_cat4e_2016=datacard_2016/hzz4l_4eS_13TeV_xs_pT4l_bin3_v3.txt hzz_PTH_80p0_120p0_cat4e_2016=datacard_2016/hzz4l_4eS_13TeV_xs_pT4l_bin4_v3.txt hzz_PTH_120p0_200p0_cat4e_2016=datacard_2016/hzz4l_4eS_13TeV_xs_pT4l_bin5_v3.txt hzz_PTH_200p0_350p0_cat4e_2016=datacard_2016/hzz4l_4eS_13TeV_xs_pT4l_bin6_v3.txt hzz_PTH_350p0_450p0_cat4e_2016=datacard_2016/hzz4l_4eS_13TeV_xs_pT4l_bin7_v3.txt hzz_PTH_GT450_cat4e_2016=datacard_2016/hzz4l_4eS_13TeV_xs_pT4l_bin8_v3.txt hzz_PTH_0p0_15p0_cat4mu_2016=datacard_2016/hzz4l_4muS_13TeV_xs_pT4l_bin0_v3.txt hzz_PTH_15p0_30p0_cat4mu_2016=datacard_2016/hzz4l_4muS_13TeV_xs_pT4l_bin1_v3.txt hzz_PTH_30p0_45p0_cat4mu_2016=datacard_2016/hzz4l_4muS_13TeV_xs_pT4l_bin2_v3.txt hzz_PTH_45p0_80p0_cat4mu_2016=datacard_2016/hzz4l_4muS_13TeV_xs_pT4l_bin3_v3.txt hzz_PTH_80p0_120p0_cat4mu_2016=datacard_2016/hzz4l_4muS_13TeV_xs_pT4l_bin4_v3.txt hzz_PTH_120p0_200p0_cat4mu_2016=datacard_2016/hzz4l_4muS_13TeV_xs_pT4l_bin5_v3.txt hzz_PTH_200p0_350p0_cat4mu_2016=datacard_2016/hzz4l_4muS_13TeV_xs_pT4l_bin6_v3.txt hzz_PTH_350p0_450p0_cat4mu_2016=datacard_2016/hzz4l_4muS_13TeV_xs_pT4l_bin7_v3.txt hzz_PTH_GT450_cat4mu_2016=datacard_2016/hzz4l_4muS_13TeV_xs_pT4l_bin8_v3.txt hzz_PTH_0p0_15p0_cat2e2mu_2016=datacard_2016/hzz4l_2e2muS_13TeV_xs_pT4l_bin0_v3.txt hzz_PTH_15p0_30p0_cat2e2mu_2016=datacard_2016/hzz4l_2e2muS_13TeV_xs_pT4l_bin1_v3.txt hzz_PTH_30p0_45p0_cat2e2mu_2016=datacard_2016/hzz4l_2e2muS_13TeV_xs_pT4l_bin2_v3.txt hzz_PTH_45p0_80p0_cat2e2mu_2016=datacard_2016/hzz4l_2e2muS_13TeV_xs_pT4l_bin3_v3.txt hzz_PTH_80p0_120p0_cat2e2mu_2016=datacard_2016/hzz4l_2e2muS_13TeV_xs_pT4l_bin4_v3.txt hzz_PTH_120p0_200p0_cat2e2mu_2016=datacard_2016/hzz4l_2e2muS_13TeV_xs_pT4l_bin5_v3.txt hzz_PTH_200p0_350p0_cat2e2mu_2016=datacard_2016/hzz4l_2e2muS_13TeV_xs_pT4l_bin6_v3.txt hzz_PTH_350p0_450p0_cat2e2mu_2016=datacard_2016/hzz4l_2e2muS_13TeV_xs_pT4l_bin7_v3.txt hzz_PTH_GT450_cat2e2mu_2016=datacard_2016/hzz4l_2e2muS_13TeV_xs_pT4l_bin8_v3.txt hzz_PTH_0p0_15p0_cat4e_2017=datacard_2017/hzz4l_4eS_13TeV_xs_pT4l_bin0_v3.txt hzz_PTH_15p0_30p0_cat4e_2017=datacard_2017/hzz4l_4eS_13TeV_xs_pT4l_bin1_v3.txt hzz_PTH_30p0_45p0_cat4e_2017=datacard_2017/hzz4l_4eS_13TeV_xs_pT4l_bin2_v3.txt hzz_PTH_45p0_80p0_cat4e_2017=datacard_2017/hzz4l_4eS_13TeV_xs_pT4l_bin3_v3.txt hzz_PTH_80p0_120p0_cat4e_2017=datacard_2017/hzz4l_4eS_13TeV_xs_pT4l_bin4_v3.txt hzz_PTH_120p0_200p0_cat4e_2017=datacard_2017/hzz4l_4eS_13TeV_xs_pT4l_bin5_v3.txt hzz_PTH_200p0_350p0_cat4e_2017=datacard_2017/hzz4l_4eS_13TeV_xs_pT4l_bin6_v3.txt hzz_PTH_350p0_450p0_cat4e_2017=datacard_2017/hzz4l_4eS_13TeV_xs_pT4l_bin7_v3.txt hzz_PTH_GT450_cat4e_2017=datacard_2017/hzz4l_4eS_13TeV_xs_pT4l_bin8_v3.txt hzz_PTH_0p0_15p0_cat4mu_2017=datacard_2017/hzz4l_4muS_13TeV_xs_pT4l_bin0_v3.txt hzz_PTH_15p0_30p0_cat4mu_2017=datacard_2017/hzz4l_4muS_13TeV_xs_pT4l_bin1_v3.txt hzz_PTH_30p0_45p0_cat4mu_2017=datacard_2017/hzz4l_4muS_13TeV_xs_pT4l_bin2_v3.txt hzz_PTH_45p0_80p0_cat4mu_2017=datacard_2017/hzz4l_4muS_13TeV_xs_pT4l_bin3_v3.txt hzz_PTH_80p0_120p0_cat4mu_2017=datacard_2017/hzz4l_4muS_13TeV_xs_pT4l_bin4_v3.txt hzz_PTH_120p0_200p0_cat4mu_2017=datacard_2017/hzz4l_4muS_13TeV_xs_pT4l_bin5_v3.txt hzz_PTH_200p0_350p0_cat4mu_2017=datacard_2017/hzz4l_4muS_13TeV_xs_pT4l_bin6_v3.txt hzz_PTH_350p0_450p0_cat4mu_2017=datacard_2017/hzz4l_4muS_13TeV_xs_pT4l_bin7_v3.txt hzz_PTH_GT450_cat4mu_2017=datacard_2017/hzz4l_4muS_13TeV_xs_pT4l_bin8_v3.txt hzz_PTH_0p0_15p0_cat2e2mu_2017=datacard_2017/hzz4l_2e2muS_13TeV_xs_pT4l_bin0_v3.txt hzz_PTH_15p0_30p0_cat2e2mu_2017=datacard_2017/hzz4l_2e2muS_13TeV_xs_pT4l_bin1_v3.txt hzz_PTH_30p0_45p0_cat2e2mu_2017=datacard_2017/hzz4l_2e2muS_13TeV_xs_pT4l_bin2_v3.txt hzz_PTH_45p0_80p0_cat2e2mu_2017=datacard_2017/hzz4l_2e2muS_13TeV_xs_pT4l_bin3_v3.txt hzz_PTH_80p0_120p0_cat2e2mu_2017=datacard_2017/hzz4l_2e2muS_13TeV_xs_pT4l_bin4_v3.txt hzz_PTH_120p0_200p0_cat2e2mu_2017=datacard_2017/hzz4l_2e2muS_13TeV_xs_pT4l_bin5_v3.txt hzz_PTH_200p0_350p0_cat2e2mu_2017=datacard_2017/hzz4l_2e2muS_13TeV_xs_pT4l_bin6_v3.txt hzz_PTH_350p0_450p0_cat2e2mu_2017=datacard_2017/hzz4l_2e2muS_13TeV_xs_pT4l_bin7_v3.txt hzz_PTH_GT450_cat2e2mu_2017=datacard_2017/hzz4l_2e2muS_13TeV_xs_pT4l_bin8_v3.txt hzz_PTH_0p0_15p0_cat4e_2018=datacard_2018/hzz4l_4eS_13TeV_xs_pT4l_bin0_v3.txt hzz_PTH_15p0_30p0_cat4e_2018=datacard_2018/hzz4l_4eS_13TeV_xs_pT4l_bin1_v3.txt hzz_PTH_30p0_45p0_cat4e_2018=datacard_2018/hzz4l_4eS_13TeV_xs_pT4l_bin2_v3.txt hzz_PTH_45p0_80p0_cat4e_2018=datacard_2018/hzz4l_4eS_13TeV_xs_pT4l_bin3_v3.txt hzz_PTH_80p0_120p0_cat4e_2018=datacard_2018/hzz4l_4eS_13TeV_xs_pT4l_bin4_v3.txt hzz_PTH_120p0_200p0_cat4e_2018=datacard_2018/hzz4l_4eS_13TeV_xs_pT4l_bin5_v3.txt hzz_PTH_200p0_350p0_cat4e_2018=datacard_2018/hzz4l_4eS_13TeV_xs_pT4l_bin6_v3.txt hzz_PTH_350p0_450p0_cat4e_2018=datacard_2018/hzz4l_4eS_13TeV_xs_pT4l_bin7_v3.txt hzz_PTH_GT450_cat4e_2018=datacard_2018/hzz4l_4eS_13TeV_xs_pT4l_bin8_v3.txt hzz_PTH_0p0_15p0_cat4mu_2018=datacard_2018/hzz4l_4muS_13TeV_xs_pT4l_bin0_v3.txt hzz_PTH_15p0_30p0_cat4mu_2018=datacard_2018/hzz4l_4muS_13TeV_xs_pT4l_bin1_v3.txt hzz_PTH_30p0_45p0_cat4mu_2018=datacard_2018/hzz4l_4muS_13TeV_xs_pT4l_bin2_v3.txt hzz_PTH_45p0_80p0_cat4mu_2018=datacard_2018/hzz4l_4muS_13TeV_xs_pT4l_bin3_v3.txt hzz_PTH_80p0_120p0_cat4mu_2018=datacard_2018/hzz4l_4muS_13TeV_xs_pT4l_bin4_v3.txt hzz_PTH_120p0_200p0_cat4mu_2018=datacard_2018/hzz4l_4muS_13TeV_xs_pT4l_bin5_v3.txt hzz_PTH_200p0_350p0_cat4mu_2018=datacard_2018/hzz4l_4muS_13TeV_xs_pT4l_bin6_v3.txt hzz_PTH_350p0_450p0_cat4mu_2018=datacard_2018/hzz4l_4muS_13TeV_xs_pT4l_bin7_v3.txt hzz_PTH_GT450_cat4mu_2018=datacard_2018/hzz4l_4muS_13TeV_xs_pT4l_bin8_v3.txt hzz_PTH_0p0_15p0_cat2e2mu_2018=datacard_2018/hzz4l_2e2muS_13TeV_xs_pT4l_bin0_v3.txt hzz_PTH_15p0_30p0_cat2e2mu_2018=datacard_2018/hzz4l_2e2muS_13TeV_xs_pT4l_bin1_v3.txt hzz_PTH_30p0_45p0_cat2e2mu_2018=datacard_2018/hzz4l_2e2muS_13TeV_xs_pT4l_bin2_v3.txt hzz_PTH_45p0_80p0_cat2e2mu_2018=datacard_2018/hzz4l_2e2muS_13TeV_xs_pT4l_bin3_v3.txt hzz_PTH_80p0_120p0_cat2e2mu_2018=datacard_2018/hzz4l_2e2muS_13TeV_xs_pT4l_bin4_v3.txt hzz_PTH_120p0_200p0_cat2e2mu_2018=datacard_2018/hzz4l_2e2muS_13TeV_xs_pT4l_bin5_v3.txt hzz_PTH_200p0_350p0_cat2e2mu_2018=datacard_2018/hzz4l_2e2muS_13TeV_xs_pT4l_bin6_v3.txt hzz_PTH_350p0_450p0_cat2e2mu_2018=datacard_2018/hzz4l_2e2muS_13TeV_xs_pT4l_bin7_v3.txt hzz_PTH_GT450_cat2e2mu_2018=datacard_2018/hzz4l_2e2muS_13TeV_xs_pT4l_bin8_v3.txt > smH_PTH_HZZ.txt

text2workspace.py datacard_combined/smH_PTH_HZZ.txt -o ../../../CombinedWorkspaces/smH_PTH/HZZ.root -P HiggsAnalysis.CombinedLimit.PhysicsModel:multiSignalModel --PO verbose --PO 'higgsMassRange=123,127' --PO 'map=.*/smH_PTH_0p0_15p0:r_smH_PTH_0_15[1.0,0.0,3.0]' --PO 'map=.*/smH_PTH_15p0_30p0:r_smH_PTH_15_30[1.0,0.0,3.0]' --PO 'map=.*/smH_PTH_30p0_45p0:r_smH_PTH_30_45[1.0,0.0,3.0]' --PO 'map=.*/smH_PTH_45p0_80p0:r_smH_PTH_45_80[1.0,0.0,3.0]' --PO 'map=.*/smH_PTH_80p0_120p0:r_smH_PTH_80_120[1.0,0.0,3.0]' --PO 'map=.*/smH_PTH_120p0_200p0:r_smH_PTH_120_200[1.0,0.0,3.0]' --PO 'map=.*/smH_PTH_200p0_350p0:r_smH_PTH_200_350[1.0,0.0,3.0]' --PO 'map=.*/smH_PTH_350p0_450p0:r_smH_PTH_350_450[1.0,0.0,3.0]' --PO 'map=.*/smH_PTH_GT450:r_smH_PTH_GT450[1.0,0.0,3.0]'

cd /work/gallim/DifferentialCombination_home/outputs/smH_PTH/HZZ

combine --name _POSTFIT_HZZ -M MultiDimFit /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/CombinedWorkspaces/smH_PTH/HZZ.root -m 125.38 --freezeParameters MH --saveWorkspace --algo=singles --cminDefaultMinimizerStrategy 0 --setParameters r_smH_PTH_0_15=1,r_smH_PTH_15_30=1,r_smH_PTH_30_45=1,r_smH_PTH_45_80=1,r_smH_PTH_80_120=1,r_smH_PTH_200_350=1,r_smH_PTH_350_450=1,r_smH_PTH_GT450=1

for poi in r_smH_PTH_0_15 r_smH_PTH_15_30 r_smH_PTH_30_45 r_smH_PTH_45_80 r_smH_PTH_80_120 r_smH_PTH_200_350 r_smH_PTH_350_450 r_smH_PTH_GT450; do combine --name _SCAN_${poi}_HZZ -M MultiDimFit /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/CombinedWorkspaces/smH_PTH/HZZ.root -m 125.38 --freezeParameters MH --saveWorkspace --algo=grid --points=30 --cminDefaultMinimizerStrategy 0 --setParameters r_smH_PTH_0_15=1,r_smH_PTH_15_30=1,r_smH_PTH_30_45=1,r_smH_PTH_45_80=1,r_smH_PTH_80_120=1,r_smH_PTH_200_350=1,r_smH_PTH_350_450=1,r_smH_PTH_GT450=1 --redefineSignalPOIs ${poi} --floatOtherPOIs=1; done
```

*yH*

```
cd /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/Analyses/hig-21-009/forCombination

combineCards.py hzz_YH_0p0_0p15_cat4e_2016=datacard_2016/hzz4l_4eS_13TeV_xs_rapidity4l_bin0_v3.txt hzz_YH_0p15_0p3_cat4e_2016=datacard_2016/hzz4l_4eS_13TeV_xs_rapidity4l_bin1_v3.txt hzz_YH_0p3_0p45_cat4e_2016=datacard_2016/hzz4l_4eS_13TeV_xs_rapidity4l_bin2_v3.txt hzz_YH_0p45_0p6_cat4e_2016=datacard_2016/hzz4l_4eS_13TeV_xs_rapidity4l_bin3_v3.txt hzz_YH_0p6_0p75_cat4e_2016=da
tacard_2016/hzz4l_4eS_13TeV_xs_rapidity4l_bin4_v3.txt hzz_YH_0p75_0p9_cat4e_2016=datacard_2016/hzz4l_4eS_13TeV_xs_rapidity4l_bin5_v3.txt hzz_YH_0p9_1p2_cat4e_2016=datacard_2016/hzz4l_4eS_13TeV_xs_rapidity4l_bin6_v3.txt hzz_YH_1p2_1p6_cat4e_2016=datacard_2016/hzz4l_4eS_13TeV_xs_rapidity4l_bin7_v3.txt hzz_YH_1p6_2p5_cat4e_2016=datacard_2016/hzz4l_4eS_13TeV_xs_rapidity4l_bin8_v3.
txt hzz_YH_0p0_0p15_cat4mu_2016=datacard_2016/hzz4l_4muS_13TeV_xs_rapidity4l_bin0_v3.txt hzz_YH_0p15_0p3_cat4mu_2016=datacard_2016/hzz4l_4muS_13TeV_xs_rapidity4l_bin1_v3.txt hzz_YH_0p3_0p45_cat4mu_2016=datacard_2016/hzz4l_4muS_13TeV_xs_rapidity4l_bin2_v3.txt hzz_YH_0p45_0p6_cat4mu_2016=datacard_2016/hzz4l_4muS_13TeV_xs_rapidity4l_bin3_v3.txt hzz_YH_0p6_0p75_cat4mu_2016=datacar
d_2016/hzz4l_4muS_13TeV_xs_rapidity4l_bin4_v3.txt hzz_YH_0p75_0p9_cat4mu_2016=datacard_2016/hzz4l_4muS_13TeV_xs_rapidity4l_bin5_v3.txt hzz_YH_0p9_1p2_cat4mu_2016=datacard_2016/hzz4l_4muS_13TeV_xs_rapidity4l_bin6_v3.txt hzz_YH_1p2_1p6_cat4mu_2016=datacard_2016/hzz4l_4muS_13TeV_xs_rapidity4l_bin7_v3.txt hzz_YH_1p6_2p5_cat4mu_2016=datacard_2016/hzz4l_4muS_13TeV_xs_rapidity4l_bin8
_v3.txt hzz_YH_0p0_0p15_cat2e2mu_2016=datacard_2016/hzz4l_2e2muS_13TeV_xs_rapidity4l_bin0_v3.txt hzz_YH_0p15_0p3_cat2e2mu_2016=datacard_2016/hzz4l_2e2muS_13TeV_xs_rapidity4l_bin1_v3.txt hzz_YH_0p3_0p45_cat2e2mu_2016=datacard_2016/hzz4l_2e2muS_13TeV_xs_rapidity4l_bin2_v3.txt hzz_YH_0p45_0p6_cat2e2mu_2016=datacard_2016/hzz4l_2e2muS_13TeV_xs_rapidity4l_bin3_v3.txt hzz_YH_0p6_0p75
_cat2e2mu_2016=datacard_2016/hzz4l_2e2muS_13TeV_xs_rapidity4l_bin4_v3.txt hzz_YH_0p75_0p9_cat2e2mu_2016=datacard_2016/hzz4l_2e2muS_13TeV_xs_rapidity4l_bin5_v3.txt hzz_YH_0p9_1p2_cat2e2mu_2016=datacard_2016/hzz4l_2e2muS_13TeV_xs_rapidity4l_bin6_v3.txt hzz_YH_1p2_1p6_cat2e2mu_2016=datacard_2016/hzz4l_2e2muS_13TeV_xs_rapidity4l_bin7_v3.txt hzz_YH_1p6_2p5_cat2e2mu_2016=datacard_20
16/hzz4l_2e2muS_13TeV_xs_rapidity4l_bin8_v3.txt hzz_YH_0p0_0p15_cat4e_2017=datacard_2017/hzz4l_4eS_13TeV_xs_rapidity4l_bin0_v3.txt hzz_YH_0p15_0p3_cat4e_2017=datacard_2017/hzz4l_4eS_13TeV_xs_rapidity4l_bin1_v3.txt hzz_YH_0p3_0p45_cat4e_2017=datacard_2017/hzz4l_4eS_13TeV_xs_rapidity4l_bin2_v3.txt hzz_YH_0p45_0p6_cat4e_2017=datacard_2017/hzz4l_4eS_13TeV_xs_rapidity4l_bin3_v3.txt
 hzz_YH_0p6_0p75_cat4e_2017=datacard_2017/hzz4l_4eS_13TeV_xs_rapidity4l_bin4_v3.txt hzz_YH_0p75_0p9_cat4e_2017=datacard_2017/hzz4l_4eS_13TeV_xs_rapidity4l_bin5_v3.txt hzz_YH_0p9_1p2_cat4e_2017=datacard_2017/hzz4l_4eS_13TeV_xs_rapidity4l_bin6_v3.txt hzz_YH_1p2_1p6_cat4e_2017=datacard_2017/hzz4l_4eS_13TeV_xs_rapidity4l_bin7_v3.txt hzz_YH_1p6_2p5_cat4e_2017=datacard_2017/hzz4l_4e
S_13TeV_xs_rapidity4l_bin8_v3.txt hzz_YH_0p0_0p15_cat4mu_2017=datacard_2017/hzz4l_4muS_13TeV_xs_rapidity4l_bin0_v3.txt hzz_YH_0p15_0p3_cat4mu_2017=datacard_2017/hzz4l_4muS_13TeV_xs_rapidity4l_bin1_v3.txt hzz_YH_0p3_0p45_cat4mu_2017=datacard_2017/hzz4l_4muS_13TeV_xs_rapidity4l_bin2_v3.txt hzz_YH_0p45_0p6_cat4mu_2017=datacard_2017/hzz4l_4muS_13TeV_xs_rapidity4l_bin3_v3.txt hzz_Y
H_0p6_0p75_cat4mu_2017=datacard_2017/hzz4l_4muS_13TeV_xs_rapidity4l_bin4_v3.txt hzz_YH_0p75_0p9_cat4mu_2017=datacard_2017/hzz4l_4muS_13TeV_xs_rapidity4l_bin5_v3.txt hzz_YH_0p9_1p2_cat4mu_2017=datacard_2017/hzz4l_4muS_13TeV_xs_rapidity4l_bin6_v3.txt hzz_YH_1p2_1p6_cat4mu_2017=datacard_2017/hzz4l_4muS_13TeV_xs_rapidity4l_bin7_v3.txt hzz_YH_1p6_2p5_cat4mu_2017=datacard_2017/hzz4l
_4muS_13TeV_xs_rapidity4l_bin8_v3.txt hzz_YH_0p0_0p15_cat2e2mu_2017=datacard_2017/hzz4l_2e2muS_13TeV_xs_rapidity4l_bin0_v3.txt hzz_YH_0p15_0p3_cat2e2mu_2017=datacard_2017/hzz4l_2e2muS_13TeV_xs_rapidity4l_bin1_v3.txt hzz_YH_0p3_0p45_cat2e2mu_2017=datacard_2017/hzz4l_2e2muS_13TeV_xs_rapidity4l_bin2_v3.txt hzz_YH_0p45_0p6_cat2e2mu_2017=datacard_2017/hzz4l_2e2muS_13TeV_xs_rapidity
4l_bin3_v3.txt hzz_YH_0p6_0p75_cat2e2mu_2017=datacard_2017/hzz4l_2e2muS_13TeV_xs_rapidity4l_bin4_v3.txt hzz_YH_0p75_0p9_cat2e2mu_2017=datacard_2017/hzz4l_2e2muS_13TeV_xs_rapidity4l_bin5_v3.txt hzz_YH_0p9_1p2_cat2e2mu_2017=datacard_2017/hzz4l_2e2muS_13TeV_xs_rapidity4l_bin6_v3.txt hzz_YH_1p2_1p6_cat2e2mu_2017=datacard_2017/hzz4l_2e2muS_13TeV_xs_rapidity4l_bin7_v3.txt hzz_YH_1p6
_2p5_cat2e2mu_2017=datacard_2017/hzz4l_2e2muS_13TeV_xs_rapidity4l_bin8_v3.txt hzz_YH_0p0_0p15_cat4e_2018=datacard_2018/hzz4l_4eS_13TeV_xs_rapidity4l_bin0_v3.txt hzz_YH_0p15_0p3_cat4e_2018=datacard_2018/hzz4l_4eS_13TeV_xs_rapidity4l_bin1_v3.txt hzz_YH_0p3_0p45_cat4e_2018=datacard_2018/hzz4l_4eS_13TeV_xs_rapidity4l_bin2_v3.txt hzz_YH_0p45_0p6_cat4e_2018=datacard_2018/hzz4l_4eS_1
3TeV_xs_rapidity4l_bin3_v3.txt hzz_YH_0p6_0p75_cat4e_2018=datacard_2018/hzz4l_4eS_13TeV_xs_rapidity4l_bin4_v3.txt hzz_YH_0p75_0p9_cat4e_2018=datacard_2018/hzz4l_4eS_13TeV_xs_rapidity4l_bin5_v3.txt hzz_YH_0p9_1p2_cat4e_2018=datacard_2018/hzz4l_4eS_13TeV_xs_rapidity4l_bin6_v3.txt hzz_YH_1p2_1p6_cat4e_2018=datacard_2018/hzz4l_4eS_13TeV_xs_rapidity4l_bin7_v3.txt hzz_YH_1p6_2p5_cat
4e_2018=datacard_2018/hzz4l_4eS_13TeV_xs_rapidity4l_bin8_v3.txt hzz_YH_0p0_0p15_cat4mu_2018=datacard_2018/hzz4l_4muS_13TeV_xs_rapidity4l_bin0_v3.txt hzz_YH_0p15_0p3_cat4mu_2018=datacard_2018/hzz4l_4muS_13TeV_xs_rapidity4l_bin1_v3.txt hzz_YH_0p3_0p45_cat4mu_2018=datacard_2018/hzz4l_4muS_13TeV_xs_rapidity4l_bin2_v3.txt hzz_YH_0p45_0p6_cat4mu_2018=datacard_2018/hzz4l_4muS_13TeV_x
s_rapidity4l_bin3_v3.txt hzz_YH_0p6_0p75_cat4mu_2018=datacard_2018/hzz4l_4muS_13TeV_xs_rapidity4l_bin4_v3.txt hzz_YH_0p75_0p9_cat4mu_2018=datacard_2018/hzz4l_4muS_13TeV_xs_rapidity4l_bin5_v3.txt hzz_YH_0p9_1p2_cat4mu_2018=datacard_2018/hzz4l_4muS_13TeV_xs_rapidity4l_bin6_v3.txt hzz_YH_1p2_1p6_cat4mu_2018=datacard_2018/hzz4l_4muS_13TeV_xs_rapidity4l_bin7_v3.txt hzz_YH_1p6_2p5_c
at4mu_2018=datacard_2018/hzz4l_4muS_13TeV_xs_rapidity4l_bin8_v3.txt hzz_YH_0p0_0p15_cat2e2mu_2018=datacard_2018/hzz4l_2e2muS_13TeV_xs_rapidity4l_bin0_v3.txt hzz_YH_0p15_0p3_cat2e2mu_2018=datacard_2018/hzz4l_2e2muS_13TeV_xs_rapidity4l_bin1_v3.txt hzz_YH_0p3_0p45_cat2e2mu_2018=datacard_2018/hzz4l_2e2muS_13TeV_xs_rapidity4l_bin2_v3.txt hzz_YH_0p45_0p6_cat2e2mu_2018=datacard_2018/
hzz4l_2e2muS_13TeV_xs_rapidity4l_bin3_v3.txt hzz_YH_0p6_0p75_cat2e2mu_2018=datacard_2018/hzz4l_2e2muS_13TeV_xs_rapidity4l_bin4_v3.txt hzz_YH_0p75_0p9_cat2e2mu_2018=datacard_2018/hzz4l_2e2muS_13TeV_xs_rapidity4l_bin5_v3.txt hzz_YH_0p9_1p2_cat2e2mu_2018=datacard_2018/hzz4l_2e2muS_13TeV_xs_rapidity4l_bin6_v3.txt hzz_YH_1p2_1p6_cat2e2mu_2018=datacard_2018/hzz4l_2e2muS_13TeV_xs_rap
idity4l_bin7_v3.txt hzz_YH_1p6_2p5_cat2e2mu_2018=datacard_2018/hzz4l_2e2muS_13TeV_xs_rapidity4l_bin8_v3.txt > yH_HZZ.txt
```

### HggHzz

````
combineCards.py hgg=DifferentialCombinationRun2/Analyses/hig-19-016/outdir_differential_Pt/Datacard_13TeV_differential_Pt.txt hzz=DifferentialCombinationRun2/CombinedCards/smH_PTH/HZZ.txt > DifferentialCombinationRun2/CombinedCards/smH_PTH/Hgg_HZZ.txt

text2workspace.py DifferentialCombinationRun2/CombinedCards/smH_PTH/HggHZZ.txt -o DifferentialCombinationRun2/CombinedWorkspaces/smH_PTH/HggHZZ.root -m 125.38 -P HiggsAnalysis.CombinedLimit.PhysicsModel:multiSignalModel --PO 'map=.*hgg.*/smH_PTH_0p0_5p0:r_smH_PTH_0_5[1.0,-1.0,4.0]' --PO 'map=.*hgg.*/smH_PTH_5p0_10p0:r_smH_PTH_5_10[1.0,-1.0,4.0]' --PO 'map=.*hgg.*/smH_PTH_10p0_15p0:r_smH_PTH_10_15[1.0,-1.0,4.0]' --PO 'map=.*hgg.*/smH_PTH_15p0_20p0:r_smH_PTH_15_20[1.0,-1.0,4.0]' --PO 'map=.*hgg.*/smH_PTH_20p0_25p0:r_smH_PTH_20_25[1.0,-1.0,4.0]' --PO 'map=.*hgg.*/smH_PTH_25p0_30p0:r_smH_PTH_25_30[1.0,-1.0,4.0]' --PO 'map=.*hgg.*/smH_PTH_30p0_35p0:r_smH_PTH_30_35[1.0,-1.0,4.0]' --PO 'map=.*hgg.*/smH_PTH_35p0_45p0:r_smH_PTH_35_45[1.0,-1.0,4.0]' --PO 'map=.*hgg.*/smH_PTH_45p0_60p0:r_smH_PTH_45_60[1.0,-1.0,4.0]' --PO 'map=.*hgg.*/smH_PTH_60p0_80p0:r_smH_PTH_60_80[1.0,-1.0,4.0]' --PO 'map=.*hgg.*/smH_PTH_80p0_100p0:r_smH_PTH_80_100[1.0,-1.0,4.0]' --PO 'map=.*hgg.*/smH_PTH_100p0_120p0:r_smH_PTH_100_120[1.0,-1.0,4.0]' --PO 'map=.*hgg.*/smH_PTH_120p0_140p0:r_smH_PTH_120_140[1.0,-1.0,4.0]' --PO 'map=.*hgg.*/smH_PTH_140p0_170p0:r_smH_PTH_140_170[1.0,-1.0,4.0]' --PO 'map=.*hgg.*/smH_PTH_170p0_200p0:r_smH_PTH_170_200[1.0,-1.0,4.0]' --PO 'map=.*hgg.*/smH_PTH_200p0_250p0:r_smH_PTH_200_250[1.0,-1.0,4.0]' --PO 'map=.*hgg.*/smH_PTH_250p0_350p0:r_smH_PTH_250_350[1.0,-1.0,4.0]' --PO 'map=.*hgg.*/smH_PTH_350p0_450p0:r_smH_PTH_350_450[1.0,-1.0,4.0]' --PO 'map=.*hgg.*/smH_PTH_450p0_1000p0:r_smH_PTH_GT450[1.0,-1.0,4.0]' --PO 'map=.*hzz.*/smH_PTH_0p0_15p0:merge_0=expr::merge_0("@0*0.202847367771+@1*0.39445029408+@2*0.402702338149", r_smH_PTH_0_5,r_smH_PTH_5_10,r_smH_PTH_10_15)' --PO 'map=.*hzz.*/smH_PTH_15p0_30p0:merge_1=expr::merge_1("@0*0.378364478517+@1*0.325914101837+@2*0.295721419646", r_smH_PTH_15_20,r_smH_PTH_20_25,r_smH_PTH_25_30)' --PO 'map=.*hzz.*/smH_PTH_30p0_45p0:merge_2=expr::merge_2("@0*0.542011999653+@1*0.457988000347", r_smH_PTH_30_35,r_smH_PTH_35_45)' --PO 'map=.*hzz.*/smH_PTH_45p0_80p0:merge_3=expr::merge_3("@0*0.601558026824+@1*0.398441973176", r_smH_PTH_45_60,r_smH_PTH_60_80)' --PO 'map=.*hzz.*/smH_PTH_80p0_120p0:merge_4=expr::merge_4("@0*0.609477373721+@1*0.390522626279", r_smH_PTH_80_100,r_smH_PTH_100_120)' --PO 'map=.*hzz.*/smH_PTH_120p0_200p0:merge_5=expr::merge_5("@0*0.506408451299+@1*0.319329859928+@2*0.174261688772", r_smH_PTH_120_140,r_smH_PTH_140_170,r_smH_PTH_170_200)' --PO 'map=.*hzz.*/smH_PTH_200p0_350p0:merge_6=expr::merge_6("@0*0.763443179205+@1*0.236556820795", r_smH_PTH_200_250,r_smH_PTH_250_350)' --PO 'map=.*hzz.*/smH_PTH_350p0_450p0:r_smH_PTH_350_450' --PO 'map=.*hzz.*/smH_PTH_GT450:r_smH_PTH_GT450'

````

# k-framework Interpretation

This part is a huge porcoddio of a mess. The problem with using TK's workflow as it is (already quite messy since Combine's way of dealing with models makes it impossible to have something clear and understandable) is that in his case all the decay channels had the same gen bin boundaries at the level of the datacard. This was exploited in the model itself and ALL bin boundaries were matched to ALL decay channels when performing the combination. 

In my case, this is not possible since every analysis did whatever the fuck they wanted so all the bins are different (but at least they're aligned). The best way I found to address this without having to change too much in TK's stuff was to convert every list that was referring to a quantity dependent on the binning to a dictionary where the keys are the decay channels. These items were identified in the following arguments passed to ```text2workspace.py```:

- binBoundaries
- SMXS_of_input_ws
- correlationMatrix
- theoryUncertainties

## Preliminary operations

Here I report the preliminary operations that have to be performed in order to have everything we need. They probably won't need to be repeated as their output will most likely be committed in the repo, but it's better to be safe than sorry.

- pick a working setup of TK's stuff (mine is at ```/work/gallim/diffComb2017/CMSSW_10_2_13/src/HiggsAnalysis/CombinedLimit/test/differentialCombination2017```) and open ```scalecorrelationmatrices.py```; here change ```top_exp_binning``` and ```yukawa_exp_binning``` with the binning that you want to dump correlation matrices and uncertainties for; in my case, that has to be done 5 times;
- run
```
python test.py --top_scalecorrelations --writefiles
python test.py --yukawa_scalecorrelations --writefiles
```
this will generate ```out/scalecorrelations_{DATE}_tophighpt``` and ```out/scalecorrelations_{DATE}_yukawa```; these folders are copied inside ```DifferentialCombinationRun2/TKPredictions/scalecorrelations_{DECAY_CHANNEL}```
- at this point the idea consists in generating yaml files containing the above mentioned dictionaries; from ```DifferentialCombination_home``` run 
```
python DifferentialCombinationRun2/specific_scripts/prepare_TK_metadata.py
``` 

At the same time, stupid datacards need to have a proper format for what concerns the names of the processes in order to be correctly picked up in the mapping business. This is done by running 
```
produce_TK_cards.py
```

### ttH

In the second part of the fits we need contributions from ttH. In TK's original code the predictions are in much larger bins, so we need to get new ones. This is done using Thomas' stuff.

Also note that for some reason it needs to use python3, so use the environment that can be created using ```specific_scripts/thomas_env.yml```.

```
cd DifferentialCombinationRun2/specific_scripts

python3 extractThPred.py -i /pnfs/psi.ch/cms/trivcat/store/user/gallim/DifferentialCombinationHugeSamples/dev_differential_fPA_SFsysT_signal_IA_18 -c /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/specific_scripts/splitConfig_Pt_2018.yml -o /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/TheoreticalPredictions/production_modes/ttH/theoryPred_Pt_2018_ttHJetToGG.all --applyNNLOPSweights --dumpTheoryUnc --inpathOA /pnfs/psi.ch/cms/trivcat/store/user/gallim/DifferentialCombinationHugeSamples/dev_differential_fPA_SFsysT_signal_OA_18 --totalXS --proc ttHJetToGG
```

before using, one might need to install the following packages:
```
oyaml
root_numpy
root_pandas
```

The whole thing is then taken care of in ```prepare_TK_metadata.py```, which dumps a JSON file then used by TK's model.

## Produce workspaces

The code that produces the calls to ```text2workspace.py``` is in the script ```produce_TK_workspace.py```. As a reminder for myself, the structure of the dictionaries here tries to reflect the fact that, despite having 6 different scenarios, theory files and stuff seem to depend only on the macro scenarios "Yukawa" and "Top". The other category used to discriminate is, of course, the decay channel (i.e. "Hgg", "HZZ", etc.).

```
# HggHZZHWWHtt

produce_TK_workspace.py --model Yukawa_NOTscalingbbH_couplingdependentBRs --category HggHZZHWWHtt

```

## Submit scans

Since it was a huge mess with billions of options, at the moment of writing there are individual scripts to be run from inside the output directory. Hopefully at some point I will change this.

## Plot

So far the idea is to have multiple types of plot for each scenario, but this can of course change at any moment. Here are the three main ones, taking as example the coupling dependent Yukawa scenario:

For EXPECTED, plot 1 and 2 sigma CLs for each category and the heatmap of what is specified for combination
```
plot_TK_scans.py --model yukawa_coupdep --input-dir /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/outputs/TK_scans/Yukawa_NOTscalingbbH_couplingdependentBRs --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/TK_plots --categories HggHZZHWWHtt --combination HggHZZHWWHtt --expected
```

Same as before, but everything is OBSERVED
```
plot_TK_scans.py --model yukawa_coupdep --input-dir /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/outputs/TK_scans/Yukawa_NOTscalingbbH_couplingdependentBRs --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/TK_plots --categories HggHZZHWWHtt --combination HggHZZHWWHtt
```

A bit weird, but still. Plot the heatmap of the EXPECTED and the CLs of OBSERVED. This will most likely be used for just one category
```
plot_TK_scans.py --model yukawa_coupdep --input-dir /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/outputs/TK_scans/Yukawa_NOTscalingbbH_couplingdependentBRs --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/TK_plots --categories HggHZZHWWHtt --combination HggHZZHWWHtt --expected-bkg
```

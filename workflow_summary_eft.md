# 4 - SMEFT Interpretation

## Model Studies

Using [EFTModelsStudies](https://github.com/maxgalli/EFTModelsStudies) and [EFTScalingEquations](https://github.com/maxgalli/EFTScalingEquations/tree/differentials_220506) it is possible to perform preliminary studies of the models before fitting them with the full likelihood. Starting from only one yaml file like e.g. [this one](https://gitlab.cern.ch/magalli/DifferentialCombinationRun2/-/blob/master/metadata/SMEFT/220926Atlas.yml), one can use ```plot_shape_matt_predictions.py```, ```chi_square_fitter.py``` and ```pca``` performing these studies for a single decay channel or the combination. For more detailed information, once can refer to the README of the repo mentioned before.

### Example

Let's see an example that involves all of them, using a config model file located at ```/work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/220926Atlas.yml```.

The first thing to do consists in taking a look at how the Wilson coefficients written in the config file change the pt spectrum. This can be done by running:
```
python plot_shape_matt_predictions.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05 --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/EFTModelsStudies/SMEFT/FullStudies/CMS-prelim-SMEFT-topU3l_22_05_05-220926Atlas --config-file /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/220926Atlas.yml --channel hgg --study 1D
```
by taking a look at the parabolas, one can also adjust the boundaries in the config file, avoiding to waste time in the bigger fits later! Note that with the option ```--channel hgg``` we specify that we want to perform the study using the equations derived for the bins of Hgg differential analysis in its phase space, since the rivet routines were used.

After this, one can use ```chi_square_fitter.py``` to perform a series of 1D and 2D scans using a simple chi square. The chi square is built using the mus measured with the differential analyses and their uncertainties, along with the covarance matrix computed within Combine. To perform these scans and plot the results, one can run
```
python chi_square_fitter.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05 --submodel-yaml /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/220926Atlas.yml --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/EFTModelsStudies/SMEFT/FullStudies/CMS-prelim-SMEFT-topU3l_22_05_05-220926Atlas --channels hgg
```

If we notice that some of the scans are completely flat (like in this case), it means that we have fit too many parameters and we are not sensitive to some of them. In this case we can use the Principal Components Analysis:
```
python pca.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05 --model-yaml /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/220926Atlas.yml --channels hgg --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/EFTModelsStudies/SMEFT/FullStudies/CMS-prelim-SMEFT-topU3l_22_05_05-220926Atlas
```
this will plot the rotation matrices and create new directories in the path specified by ```--prediction-dir``` (refer to the repo README for more details). For this specific case we see that there are only three eigenvectors whose eigenvalues are large enough. At this point we can write a new model config file with the same format as the one before and repeat the process:

```
python plot_shape_matt_predictions.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05_rotated220926Atlashgg --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/EFTModelsStudies/SMEFT/FullStudies/CMS-prelim-SMEFT-topU3l_22_05_05_rotated220926Atlashgg-220926AtlasEV --config-file /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/220926AtlasEV.yml --channel hgg --study 1D
```

Then we can add another channel (if we have it) and perform again the PCA:

```
python pca.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05 --model-yaml /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/220926Atlas.yml --channels hgg hzz --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/EFTModelsStudies/SMEFT/FullStudies/CMS-prelim-SMEFT-topU3l_22_05_05-220926Atlas
```

and fit:
```
python chi_square_fitter.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05_rotated220926Atlashgghzz --submodel-yaml /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/220926AtlasEVhgghzz.yml --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/EFTModelsStudies/SMEFT/FullStudies/CMS-prelim-SMEFT-topU3l_22_05_05_rotated220926Atlashgghzz-220926AtlasEVhgghzz --channels hgg hzz

```
python DifferentialCombinationRun2/specific_scripts/rename_HttBoost_related_cards.py
```

which can fail if the cards don't exist yet.


## Fits

This part refers to what happens inside [DifferentialCombinationRun2](https://gitlab.cern.ch/magalli/DifferentialCombinationRun2) and partially overlaps with the docs in there.

On 05.08.22 I started implementing the whole machinery to allow some kind of flexibility in performing fits for SMEFT coefficients. Since it is a bit convoluted, I try to write it down here so I will remember the main aspects.

The whole business revolves around the concept of **MODEL** and **SUBMODEL**. The former refers to what is, in some sense, the output ot ```text2workspace.py``` while the latter refers to which of the parameters in MODEL we want to study. The config file for a MODEL can be found in ```DifferentialCombinationRun2/metadata/SMEFT``` with name ```MODEL.yml```. A MODEL can have multiple submodels, whose configurations will be found in the same path with the name ```MODEL_SUBMODEL.yml```.

The directory structure reflects this division and the different kinds of fits that we have to perform. What fits?

- for each MODEL, the first (trivial) kind of fit that we can perform consists in allowing only one parameter to float in the Global Fit (GF) with the scans that are then performed only on that parameter; since every other parameter is not taken into account, SUBMODULES are here not taken into any consideration; last but not least, for each parameter we will have to perform the fit for multiple combinations of categories (i.e. for example Hgg, Hgg_asimov, HggHZZ, HggHZZ_asimov, etc.);
- each SUBMODEL then defines mostly which parameters we want to allow to float and which ones we want to ignore; the ones specified in the config file which refers to the SUBMODEL are allowed to float; for what concerns the scans, in this case we will have plenty of them: we will indeed, using the GF file as input, scan one parameter at a time while profiling the others and scan all the possible pairs built from the input ones.

Here we report examples of commands for the first:

```
submit_SMEFT_scans.py --observable smH_PTH --category Hgg_asimov --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SMEFT/smH_PTH/220804Mod --output-dir DifferentialCombinationRun2/outputs/SMEFT_scans --base-model DifferentialCombinationRun2/metadata/SMEFT/220804Mod.yml --submodel chg
```

this command will dump all the outputs in the directory ```smH_PTH/220804Mod/FreezeOthers_chg/Hgg_asimov-20220805xxx163012```; note in particular ```Freezeothers_chg```.

For what concerns the second, and example is the following:

```
submit_SMEFT_scans.py --observable smH_PTH --category Hgg_asimov --input-dir DifferentialCombinationRun2/CombinedWorkspaces/SMEFT/smH_PTH/220804Mod --output-dir DifferentialCombinationRun2/outputs/SMEFT_scans --base-model DifferentialCombinationRun2/metadata/SMEFT/220804Mod.yml --submodel DifferentialCombinationRun2/metadata/SMEFT/220804Mod_220805test.yml
```

which will dump results in the directory ```smH_PTH/220804Mod/220805test/Hgg_asimov-20220805xxx165259```. The scan ROOT files will have names like e.g. ```higgsCombine_SCAN_1Dchb.POINTS.0.0.MultiDimFit.mH125.38.root``` for a 1D scan of ```chb``` and ```higgsCombine_SCAN_2Dchb-chw.POINTS.143.143.MultiDimFit.mH125.38.root``` for a 2D scan of ```chb``` and ```chw```. 

## Plotting

Also for plotting, the two division into the two kinds of fit is kept. We will thus have, following the example frmo before:
```
plot_SMEFT_scans.py --how freezeothers --model 220804Mod --input-dir DifferentialCombinationRun2/outputs/SMEFT_scans --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/SMEFT_plots --categories Hgg

plot_SMEFT_scans.py --how submodel --model 220804Mod --submodel DifferentialCombinationRun2/metadata/SMEFT/220804Mod_220805test.yml --input-dir DifferentialCombinationRun2/outputs/SMEFT_scans --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/SMEFT_plots --categories Hgg
```


## Very detailed example of a full study following pruning and PCA with combination

The first thing to do consists in picking a handful of operators directly from the equations.

```
cd /work/gallim/DifferentialCombination_home/EFTScalingEquations
python scripts_differentials/yaml_from_pruning.py --prediction-dir equations/CMS-prelim-SMEFT-topU3l_22_05_05 --output-file /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/221121PruneNoCP.yml --new-dir --threshold 0.0001
```

this command will dump a config file at the path specified by ```--output-file``` and a new directory at ```equations/CMS-prelim-SMEFT-topU3l_22_05_05_pruned0p0001``` keeping only those parts of the equations for which the coefficient is above the specified threshold. We can now modify the config file on our needs.

Then we need to perform PCA and plot the relevant information.
```
cd /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/EFTModelsStudies/SMEFT/FullStudies
mkdir CMS-prelim-SMEFT-topU3l_22_05_05_pruned0p0001-221121PruneNoCP
```

this is important in order not to get lost with the naming convention. We create a directory where the scheme is ```$name of prediction dir$-$name of config file$```

First plot parabolas just to understand what we are doing:
```
python plot_shape_matt_predictions.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05_pruned0p0001 --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/EFTModelsStudies/SMEFT/FullStudies/CMS-prelim-SMEFT-topU3l_22_05_05_pruned0p0001-221121PruneNoCP --config-file /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/221121PruneNoCP.yml --channels hgg hzz hww htt hbbvbf httboost --skip-spectra
```

then PCA:

```
python pca.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05_pruned0p0001 --model-yaml /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/221121PruneNoCP.yml --channels hgg hzz hww htt hbbvbf httboost --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/EFTModelsStudies/SMEFT/FullStudies/CMS-prelim-SMEFT-topU3l_22_05_05_pruned0p0001-221121PruneNoCP
```
note that a new directory with new predictions has been created at ```/work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05_pruned0p0001_rotated221121PruneNoCPhgghzzhwwhtthbbvbfhttboost```. The naming convention tries to incorporate the original basis (```CMS-prelim-SMEFT-topU3l_22_05_05_pruned0p0001```) the config file used (```rotated221121PruneNoCP```) and the list of decay channels that we spacified when doing the PCA (```hgghzzhwwhtthbbvbfhttboost```).

We can now prune once again with:
```
python scripts_differentials/yaml_from_pruning.py --prediction-dir equations/CMS-prelim-SMEFT-topU3l_22_05_05_pruned0p0001_rotated221121PruneNoCPhgghzzhwwhtthbbvbfhttboost --output-file /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/221121PruneNoCPEVhgghzzhwwhtthbbvbfhttboost.yml --new-dir --threshold 0.0001
```

then comment out the parameters in the new config file **according to which ones have higher eigenvalue**.

```
cd /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/EFTModelsStudies/SMEFT/FullStudies
mkdir CMS-prelim-SMEFT-topU3l_22_05_05_pruned0p0001_rotated221121PruneNoCPhgghzzhwwhtthbbvbfhttboost_pruned0p0001-221121PruneNoCPEVhgghzzhwwhtthbbvbfhttboost
```

and repeat the procedure to study the parabolas:
```
python plot_shape_matt_predictions.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05_pruned0p0001_rotated221121PruneNoCPhgghzzhwwhtthbbvbfhttboost_pruned0p0001 --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/EFTModelsStudies/SMEFT/FullStudies/CMS-prelim-SMEFT-topU3l_22_05_05_pruned0p0001_rotated221121PruneNoCPhgghzzhwwhtthbbvbfhttboost_pruned0p0001-221121PruneNoCPEVhgghzzhwwhtthbbvbfhttboost --config-file /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/221121PruneNoCPEVhgghzzhwwhtthbbvbfhttboost.yml --channels hgg hzz hww htt hbbvbf httboost --skip-spectra
```

After fine tuning the ranges, we can proceed with a chi square fit:
```
time python chi_square_fitter.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05_pruned0p0001_rotated221121PruneNoCPhgghzzhwwhtthbbvbfhttboost_pruned0p0001 --submodel-yaml /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/221121PruneNoCPEVhgghzzhwwhtthbbvbfhttboost.yml --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/EFTModelsStudies/SMEFT/FullStudies/CMS-prelim-SMEFT-topU3l_22_05_05_pruned0p0001_rotated221121PruneNoCPhgghzzhwwhtthbbvbfhttboost_pruned0p0001-221121PruneNoCPEVhgghzzhwwhtthbbvbfhttboost --channels hgg hzz hww htt hbbvbf httboost --multiprocess --skip2D
```

**A quick note**: while writing this part, I also tried to do the same without pruning (i.e. using the original equations in both cases) and there seem to be almost no difference. I can probably skip that part and use the ```EFTScalingEquations``` scripts just to select the operators.


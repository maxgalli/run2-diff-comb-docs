# 4 - SMEFT Interpretation

## Model Studies

Using [EFTModelsStudies](https://github.com/maxgalli/EFTModelsStudies) and [EFTScalingEquations](https://github.com/maxgalli/EFTScalingEquations/tree/differentials_220506) it is possible to perform preliminary studies of the models before fitting them with the full likelihood. Starting from only one yaml file like e.g. [this one](https://gitlab.cern.ch/magalli/DifferentialCombinationRun2/-/blob/master/metadata/SMEFT/220926Atlas.yml), one can use ```plot_shape_matt_predictions.py```, ```chi_square_fitter.py``` and ```pca``` performing these studies for a single decay channel or the combination. For more detailed information, once can refer to the README of the repo mentioned before.

### Example

Let's see an example that involves all of them, using a config model file located at ```/work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/221026Tutorial.yml```.

The first thing to do consists in taking a look at how the Wilson coefficients written in the config file change the pt spectrum. This can be done by running:
```
python plot_shape_matt_predictions.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05 --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/EFTModelsStudies/SMEFT/FullStudies/CMS-prelim-SMEFT-topU3l_22_05_05-221026Tutorial --config-file /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/221026Tutorial.yml --channel hgg --study 1D
```
by taking a look at the parabolas, one can also adjust the boundaries in the config file, avoiding to waste time in the bigger fits later! Note that with the option ```--channel hgg``` we specify that we want to perform the study using the equations derived for the bins of Hgg differential analysis in its phase space, since the rivet routines were used.

After this, one can use ```chi_square_fitter.py``` to perform a series of 1D and 2D scans using a simple chi square. The chi square is built using the mus measured with the differential analyses and their uncertainties, along with the covarance matrix computed within Combine. To perform these scans and plot the results, one can run
```
python chi_square_fitter.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05 --submodel-yaml /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/221026Tutorial.yml --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/EFTModelsStudies/SMEFT/FullStudies/CMS-prelim-SMEFT-topU3l_22_05_05-221026Tutorial --channels hgg
```



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




## Most updated wrapper commands per category (to be regularly updated)

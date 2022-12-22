# Paper and AN Plots Commands

## Analysis Note

PCA related plots:

```
python pca.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05 --model-yaml /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/221220PruneNoCP.yml --channels hgg hzz hww htt hbbvbf httboost --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/EFTModelsStudies/SMEFT/FullStudies --how A
```

Pruned scans:
```
plot_two_scans.py --input-dir /work/gallim/DifferentialCombination_home/checks/221219_envelope_pruning/hig-19-016/outdir_differential_PtInclusive --label Original --other-input-dir /work/gallim/DifferentialCombination_home/checks/221219_envelope_pruning/pruned_outdir_differential_PtInclusive --other-label Pruned --poi r0 --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/AN_plots/other --file-name-tmpl higgsCombineDataScan_r0*
```
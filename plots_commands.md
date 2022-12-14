# Paper and AN Plots Commands

## Analysis Note

SM plots:

```
plot_xs_scans.py --observable smH_PTH --input-dir outputs/SM_scans/smH_PTH --metadata-dir DifferentialCombinationRun2/metadata/xs_POIs/SM --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/SM_plots --categories HggHZZHWWHttHbbVBFHttBoost Hgg HZZ HWW --singles Htt HbbVBF HttBoost --config-file DifferentialCombinationRun2/metadata/xs_POIs/SM/smH_PTH/plot_config.yml --systematic-bands HggHZZHWWHttHbbVBFHttBoost

plot_xs_scans.py --observable smH_PTH --input-dir outputs/SM_scans/smH_PTH --metadata-dir DifferentialCombinationRun2/metadata/xs_POIs/SM --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/SM_plots --categories HggHZZHWWHttHbbVBFHttBoost --config-file DifferentialCombinationRun2/metadata/xs_POIs/SM/smH_PTH/plot_config.yml --systematic-bands HggHZZHWWHttHbbVBFHttBoost

plot_xs_scans.py --observable smH_PTJ0 --input-dir outputs/SM_scans/smH_PTJ0 --metadata-dir DifferentialCombinationRun2/metadata/xs_POIs/SM --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/SM_plots --categories HggHZZHttBoost Hgg HZZ --singles HttBoost --config-file DifferentialCombinationRun2/metadata/xs_POIs/SM/smH_PTJ0/plot_config.yml

plot_xs_scans.py --observable Njets --input-dir outputs/SM_scans/Njets --metadata-dir DifferentialCombinationRun2/metadata/xs_POIs/SM --output-dir outputs/SM_plots --categories HggHZZHWWHtt Hgg HZZ HWW --singles Htt --systematic-bands HggHZZHWWHtt --config-file DifferentialCombinationRun2/metadata/xs_POIs/SM/Njets/plot_config.yml --allow-extrapolation

plot_xs_scans.py --observable yH --input-dir outputs/SM_scans/yH --metadata-dir DifferentialCombinationRun2/metadata/xs_POIs/SM --output-dir outputs/SM_plots --categories HggHZZ Hgg HZZ --config-file DifferentialCombinationRun2/metadata/xs_POIs/SM/yH/plot_config.yml --systematic-bands HggHZZ

plot_xs_scans.py --observable DEtajj --input-dir outputs/SM_scans/DEtajj --metadata-dir DifferentialCombinationRun2/metadata/xs_POIs/SM --output-dir outputs/SM_plots --categories HggHZZ Hgg HZZ --config-file DifferentialCombinationRun2/metadata/xs_POIs/SM/DEtajj/plot_config.yml --systematic-bands HggHZZ

plot_xs_scans.py --observable mjj --input-dir outputs/SM_scans/mjj --metadata-dir DifferentialCombinationRun2/metadata/xs_POIs/SM --output-dir outputs/SM_plots --categories HggHZZ Hgg HZZ --config-file DifferentialCombinationRun2/metadata/xs_POIs/SM/mjj/plot_config.yml --systematic-bands HggHZZ

plot_xs_scans.py --observable TauCJ --input-dir outputs/SM_scans/TauCJ --metadata-dir DifferentialCombinationRun2/metadata/xs_POIs/SM --output-dir outputs/SM_plots --categories HggHZZ Hgg HZZ --config-file DifferentialCombinationRun2/metadata/xs_POIs/SM/TauCJ/plot_config.yml --systematic-bands HggHZZ

# matrices
plot_matrices.py --rfr-file outputs/SM_scans/smH_PTH/HggHZZHWWHttHbbVBFHttBoost_asimov-20221210xxx193718/multidimfitAsimovBestFit.root --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/AN_plots/SM/results/ --pois DifferentialCombinationRun2/metadata/xs_POIs/SM/smH_PTH/HggHZZHWWHttHbbVBFHttBoost.yml --suffix _smH_PTH_expected --observable smH_PTH

plot_matrices.py --rfr-file outputs/SM_scans/smH_PTJ0/HggHZZHttBoost_asimov-20221229xxx095201/multidimfitAsimovBestFit.root --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/AN_plots/SM/results/ --pois DifferentialCombinationRun2/metadata/xs_POIs/SM/smH_PTJ0/HggHZZHttBoost.yml --suffix _smH_PTJ0_expected --observable smH_PTJ0

plot_matrices.py --rfr-file DifferentialCombinationRun2/outputs/matrices/SM/Njets/HggHZZHWWHtt/obs/multidimfit_POSTFIT_HggHZZHWWHtt.root --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/AN_plots/SM/results/ --pois DifferentialCombinationRun2/metadata/xs_POIs/SM/Njets/HggHZZHWWHtt.yml --suffix _Njets_obs --observable Njets

plot_matrices.py --rfr-file DifferentialCombinationRun2/outputs/matrices/SM/yH/HggHZZ/obs/multidimfit_POSTFIT_HggHZZ.root --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/AN_plots/SM/results/ --pois DifferentialCombinationRun2/metadata/xs_POIs/SM/yH/HggHZZ.yml --suffix _yH_obs --observable yH

plot_matrices.py --rfr-file DifferentialCombinationRun2/outputs/matrices/SM/DEtajj/HggHZZ/obs/multidimfit_POSTFIT_HggHZZ.root --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/AN_plots/SM/results/ --pois DifferentialCombinationRun2/metadata/xs_POIs/SM/DEtajj/HggHZZ.yml --suffix _DEtajj_obs --observable DEtajj

plot_matrices.py --rfr-file DifferentialCombinationRun2/outputs/matrices/SM/mjj/HggHZZ/obs/multidimfit_POSTFIT_HggHZZ.root --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/AN_plots/SM/results/ --pois DifferentialCombinationRun2/metadata/xs_POIs/SM/mjj/HggHZZ.yml --suffix _mjj_obs --observable mjj

plot_matrices.py --rfr-file DifferentialCombinationRun2/outputs/matrices/SM/TauCJ/HggHZZ/obs/multidimfit_POSTFIT_HggHZZ.root --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/AN_plots/SM/results/ --pois DifferentialCombinationRun2/metadata/xs_POIs/SM/TauCJ/HggHZZ.yml --suffix _TauCJ_obs --observable TauCJ
```

PCA related plots:

```
python pca.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05 --model-yaml /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/221220PruneNoCP.yml --channels hgg hzz hww htt hbbvbf httboost --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/EFTModelsStudies/SMEFT/FullStudies --how A
```

```
plot_SMEFT_scans.py --how submodel --model 221220PruneNoCPEVhgghzzhwwhtthbbvbfhttboostLinearised --submodel DifferentialCombinationRun2/metadata/SMEFT/221220PruneNoCPEVhgghzzhwwhtthbbvbfhttboostLinearised.yml --input-dir outputs/SMEFT_scans --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/SMEFT_plots --categories HggHZZHWWHttHbbVBFHttBoost --combination HggHZZHWWHttHbbVBFHttBoost --expected --skip-2d --config-file DifferentialCombinationRun2/metadata/SMEFT/plot_config.yml
```

```
plot_SMEFT_linquad.py --linear /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/221220PruneNoCPEVhgghzzhwwhtthbbvbfhttboostLinearised.yml --quad /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/221220PruneNoCPEVhgghzzhwwhtthbbvbfhttboostNonLinear.yml --input-dir outputs/SMEFT_scans --category HggHZZHWWHttHbbVBFHttBoost_asimov --observable smH_PTH --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/SMEFT_plots/LinQuad --config-file DifferentialCombinationRun2/metadata/SMEFT/plot_config.yml
```

```
plot_matrices.py --rfr-file outputs/SMEFT_scans/smH_PTH/221220PruneNoCPEVhgghzzhwwhtthbbvbfhttboostLinearised/221220PruneNoCPEVhgghzzhwwhtthbbvbfhttboostLinearised/HggHZZHWWHttHbbVBFHttBoost_asimov-20221229xxx162930/multidimfitAsimovBestFit.root --output-dir outputs/SMEFT_plots/smH_PTH/221220PruneNoCPEVhgghzzhwwhtthbbvbfhttboostLinearised/221220PruneNoCPEVhgghzzhwwhtthbbvbfhttboostLinearised --pois EV0 EV1 EV2 EV3 EV4 EV5 EV6
```

```
python DifferentialCombinationPostProcess/specific_scripts/plot_rotation_example_chi.py
```

Mega SMEFT tables:

```
python print_latex_equations.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05_rotated221220PruneNoCPhgghzzhwwhtthbbvbfhttboostA --submodel-yaml /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/221220PruneNoCPEVhgghzzhwwhtthbbvbfhttboostLinearised.yml --channels hgg hzz hww htt hbbvbf httboost --fit-model full

python print_latex_equations.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05_rotated221220PruneNoCPhgghzzhwwhtthbbvbfhttboostA --submodel-yaml /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/221220PruneNoCPEVhgghzzhwwhtthbbvbfhttboostLinearised.yml --channels hgg hzz hww htt hbbvbf httboost --fit-model linearised

python print_latex_equations_ev.py --input-file /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/EFTModelsStudies/SMEFT/FullStudies/CMS-prelim-SMEFT-topU3l_22_05_05-221220PruneNoCP/PCA-hgghzzhwwhtthbbvbfhttboost-Full-A.json
```

Pruned scans:

```
plot_two_scans.py --input-dir /work/gallim/DifferentialCombination_home/checks/221219_envelope_pruning/hig-19-016/outdir_differential_PtInclusive --label Original --other-input-dir /work/gallim/DifferentialCombination_home/checks/221219_envelope_pruning/pruned_outdir_differential_PtInclusive --other-label Pruned --poi r0 --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/AN_plots/other --file-name-tmpl higgsCombineDataScan_r0*
```
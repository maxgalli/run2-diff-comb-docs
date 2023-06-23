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
python pca.py \
--prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-ForDiff-230530 \
--model-yaml /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/230620PruneNoCP.yml \
--config /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/config/PtFullComb.json \
--output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/EFTModelsStudies/SMEFT/FullStudies \
--how A
```

```
plot_SMEFT_scans.py \
--how submodel \
--model 230620PruneNoCPEVPtFullCombLinearised \
--submodel DifferentialCombinationRun2/metadata/SMEFT/230620PruneNoCPEVPtFullCombLinearised.yml \
--input-dir outputs/SMEFT_scans \
--output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/SMEFT_plots \
--categories PtFullComb \
--combination PtFullComb \
--expected \
--skip-2d \
--config-file DifferentialCombinationRun2/metadata/SMEFT/plot_config.yml
```

```
plot_matrices.py \
--rfr-file outputs/SMEFT_scans/230620PruneNoCPEVPtFullCombLinearised/230620PruneNoCPEVPtFullCombLinearised/PtFullComb_asimov-20230620xxx190728/multidimfitAsimovBestFit.root \
--output-dir outputs/SMEFT_plots/230620PruneNoCPEVPtFullCombLinearised/230620PruneNoCPEVPtFullCombLinearised \
--pois EV0 EV1 EV2 EV3 EV4 EV5 EV6 EV7 EV8 EV9 EV10 EV11
```

```
python DifferentialCombinationPostProcess/specific_scripts/plot_rotation_example_chi.py
```

Equations for deltaphijj:

```
python print_latex_equations.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-ForDiffAllSMEFTsim-230530-FullDecay --submodel-yaml /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/230611AtlasDPJ_ChgScen.yml --fit-model full --config /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/config/DeltaPhiJJHggHZZ.json

python print_latex_equations.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-ForDiff-230530-FullDecay --submodel-yaml /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/230611AtlasDPJ_ChwScen.yml --fit-model full --config /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/config/DeltaPhiJJHggHZZ.json

python print_latex_equations.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-ForDiff-230530-FullDecay --submodel-yaml /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/230611AtlasDPJ_ChbScen.yml --fit-model full --config /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/config/DeltaPhiJJHggHZZ.json

python print_latex_equations.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-ForDiff-230530-FullDecay --submodel-yaml /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/230611AtlasDPJ_ChwbScen.yml --fit-model full --config /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/config/DeltaPhiJJHggHZZ.json
```

Mega SMEFT tables:

```
python print_latex_equations.py \
--prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-ForDiff-230530_rotated230620PruneNoCPPtFullCombA \
--submodel-yaml /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/230620PruneNoCPEVPtFullCombLinearised.yml \
--fit-model linearised \
--config /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/config/PtFullComb.json

python print_latex_equations_ev.py \
--input-file /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/EFTModelsStudies/SMEFT/FullStudies/CMS-ForDiff-230530-230620PruneNoCP-PtFullComb/PCA-hgghzzhwwhtthbbvbfhttboost-Full-A.json
```

Pruned scans:

```
plot_two_scans.py --input-dir /work/gallim/DifferentialCombination_home/checks/221219_envelope_pruning/hig-19-016/outdir_differential_PtInclusive --label Original --other-input-dir /work/gallim/DifferentialCombination_home/checks/221219_envelope_pruning/pruned_outdir_differential_PtInclusive --other-label Pruned --poi r0 --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/AN_plots/other --file-name-tmpl higgsCombineDataScan_r0*
```

SMEFT Partial Decay Width Parametrization

```
cd /work/gallim/DifferentialCombination_home/EFTScalingEquations/scripts_differentials
python plot_matt_check.py
```

```
python chi_square_fitter.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05_AccCorr --submodel /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/230606_CombTest.yml --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/Checks/230615_mattchecks --config config/PtHZZ.json --fit-model full --skip2D
python chi_square_fitter.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05_AccCorr --submodel /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/230606_CombTest.yml --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/Checks/230615_mattchecks --config config/PtHZZ.json --fit-model full --skip2D --suffix _split
python chi_square_fitter.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05_AccCorr --submodel /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/230606_CombTestW.yml --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/Checks/230615_mattchecks --config config/PtHWW.json --fit-model full --skip2D
python chi_square_fitter.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05_AccCorr --submodel /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/230606_CombTestW.yml --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/Checks/230615_mattchecks --config config/PtHWW.json --fit-model full --skip2D --suffix _split
python chi_square_fitter.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05_AccCorr --submodel /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/230606_CombTest.yml --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/Checks/230615_mattchecks --config config/PtHggHZZ.json --fit-model full --skip2D
python chi_square_fitter.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05_AccCorr --submodel /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/230606_CombTest.yml --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/Checks/230615_mattchecks --config config/PtHggHZZ.json --fit-model full --skip2D --suffix _split
python chi_square_fitter.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05_AccCorr --submodel /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/230606_CombTestW.yml --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/Checks/230615_mattchecks --config config/PtHggHWW.json --fit-model full --skip2D
python chi_square_fitter.py --prediction-dir /work/gallim/DifferentialCombination_home/EFTScalingEquations/equations/CMS-prelim-SMEFT-topU3l_22_05_05_AccCorr --submodel /work/gallim/DifferentialCombination_home/DifferentialCombinationRun2/metadata/SMEFT/230606_CombTestW.yml --output-dir /eos/home-g/gallim/www/plots/DifferentialCombination/CombinationRun2/Checks/230615_mattchecks --config config/PtHggHWW.json --fit-model full --skip2D --suffix _split

python tries/230615_CheckaAN.py
```
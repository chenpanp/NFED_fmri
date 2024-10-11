#A fMRI dataset in response to large-scale short natural dynamic facial expression videos
Facial expression is among the most natural methods for human beings to convey their emotional information in daily life. Although the neural mechanism of facial expression has been extensively studied employing lab-controlled images and a small number of lab-controlled video stimuli, how the human brain processes natural facial expressions still needs to be investigated. To our knowledge, this type of data specifically on large-scale natural facial expression videos is currently missing. We describe here the natural Facial Expressions Dataset (NFED), a fMRI dataset including responses to 1,320 short (3-second) natural facial expression video clips. These video clips is annotated with three types of labels: emotion, gender, and ethnicity, along with accompanying metadata. We validate that the dataset has good quality within and across participants and, notably, can capture temporal and spatial stimuli features. NFED provides researchers with fMRI data for understanding of the visual processing of large number of natural facial expression videos.
The dataset contains raw data, pre-processed volume data,pre-processed  surface data and suface-based analyzed data.
To get more details, please refer to the paper at {website} and the dataset at https://openneuro.org/datasets/ds005047

## Preprocess procedure
We adopted the data analysis method proposed by Kay et al(http://surfer.nmr.mgh.harvard.edu). The FreeSurfer software was utilized to generate participants’ pial and white surfaces based on the T1 volume. Furthermore, for each participant, we constructed an intermediate gray matter surface between the pial and the white surfaces.
We employed the FSL utility “prelude” to phase-unwrap field maps obtained during each scan session. To regularize the field maps, we then used an Epanechnikov kernel to perform three-dimensional local linear regression. In the regression, we employed the magnitude component values from the field maps as weights to enhance the field estimates’ robustness. This regularization process eliminates noise in the field maps while imposing spatial smoothness. Ultimately, The field maps underwent linear interpolation over time, resulting in an estimation of the field strength for all functional volumes.
We initially pre-processed the task-based functional data based on volume by executing one spatial resampling and one temporal resampling. For the purpose of EPI distortion and head motion correction, we performed one spatial resampling. The correction for slice timing was achieved through temporal resampling, which involved performing one cubic interpolation on the time series of each voxel. The EPI spatial distortion was corrected by using regularized time-interpolated field maps. The SPM12 utility "spm_realign" was then employed to estimate the parameters of rigid-body motion from the undistorted EPI volumes. Ultimately, we used one cubic interpolation on each volume that had been corrected for slice timing to realize the spatial resampling.
The mean attained by pre-processing functional volumes in a session was co-registered to the T1 volume. A three-dimensional ellipse defined manually was employed to concentrate cost metric on brain areas that were not impacted by gross susceptibility influences during the co-registration alignment estimation. The ultimate outcome of the co-registration process is a transformation which illustrates how to align the EPI data with the native anatomy of the participants.
We used pre-processing based on surface to re-analyze the functional data following the completion of co-registration for the anatomical data. This two-stage method was adopted due to the necessity of volume-based pre-processing to produce undistorted functional volumes of high quality, which is essential for registering the functional data with the anatomical data. Surface-based pre-processing can only begin after obtaining this registration.
During pre-processing based on surface, the processes are identical to those of volume-based pre-processing, with the only difference being that the ultimate spatial interpolation is conducted at the vertices of the intermediate gray matter surfaces. Therefore, the sole distinction lies in whether the data is being prepared on an irregular manifold of densely spaced vertices (surface) or a regular three-dimensional grid (volume). The procedures of surface-based pre-processing can finally be simplified to one spatial resampling (for addressing EPI distortion, head motion, and registration to anatomy) and one temporal resampling (for managing slice acquisition times). The advantage of conducting only two simple pre-processing steps is to avoid unnecessary interpolation and maximize the preservation of spatial resolution. The time-series data were ultimately generated for every vertex of the cortical surfaces after completing this pre-processing stage.

**code: https://openneuro.org/datasets/ds005047/derivatives/validation/code/volume_pre-process/**
**code: https://openneuro.org/datasets/ds005047/derivatives/validation/code/surface_pre-process/**
Detailed usage notes are available in codes, please read carefully and modify variables to satisfy your customed environment.
## GLM of main experiment
We utilized a single-trial General Linear Model (GLMsingle) (https://github.com/cvnlab/GLMsingle) approach, an advanced denoising approach in MATLAB R2019a, to model the pre-processed fMRI data from main experiment. For single trials, the method of GLM was developed to offer estimations of BOLD response magnitudes ('betas'). GLMsingle requires only fMRI time series data and a design matrix as inputs, integrating three techniques to enhance the accuracy of experimental GLM beta estimates. Firstly, for each voxel, a custom HRF is identified from a library of candidate functions. Secondly, cross-validation is utilized to derive a set of noise regressors from voxels unrelated to the experimental paradigm. Thirdly, to improve the stability of beta estimates for closely spaced trials, ridge regression is employed on a voxel-wise basis to regularize the betas. In this study, three betas were calculated by analyzing the BOLD response corresponding to individual video onset ranging from 1 to 3 seconds with 1-second intervals. We produced individual GLMsingle models for each session (consisted of 4 training runs and 2 test runs). In general, for each video within the training set, 2 (repetitions) x 3 (seconds) betas were acquired. Similarly, for each video within the testing set, 10 (repetitions) x 3 (seconds) betas were acquired. The utilization of repetitions enabled us to acquire video-evoked responses with a high signal-to-noise ratio (SNR).

The regressors in the GLMsingle toolbox mainly include the following categories:
1.Experimental Design Matrix: This is constructed based on the experimental design, including experimental conditions, task events, etc., and is used to estimate the BOLD (Blood - Oxygen - Level - Dependent) signal response.
2.Data-driven nuisance regressors: The data-driven nuisance regressors used in the GLMdenoise technique. These are identified by analyzing the data itself and are used to remove noise and improve the accuracy of beta estimation.
3.Physiological noise regressors: May include indicators of physiological signals such as heart rate and respiration, and are used to correct the influence of physiological noise on the BOLD signal.
4.Movement parameter regressors: Usually include head movement parameters, such as translation and rotation, and are used to correct signal changes caused by head movement.
5.Polynomial regressors: Polynomial terms used to o characterize the baseline signal level.


**code: https://openneuro.org/datasets/ds005047/derivatives/validation/code/GLMsingle-main-experiment/matlab/NFED_GLMsingle.m**

#### retinotopic mapping

The fMRI data from the the population receptive field experiment were analyzed by a pRF model implemented in the analyzePRF toolbox (http://cvnlab.net/analyzePRF/) to characterize individual retinotopic representation. Make sure to download required software mentioned in the code.

**code: https://openneuro.org/datasets/ds005047/derivatives/validation/code/Functional-localizer-experiment-analysis/s4a_analysis_prf.m**

#### fLoc experiment

We used GLMdenoise,a data-driven denoising method,to analyze the pre-processed fMRI data from the fLoc experiment.We used a "condition-split" strategy to code the 10 stimulus categories, splitting the trials related to each category into individual conditions in each run. Six response estimates (beta values) for each category were produced by using six condition-splits.To quantify selectivity for various categories and domains,we computed t-values using the GLM beta values after fitting the GLM.The regions of interest with category selectivity for each participant were defined by using the resulting maps.
**code: https://openneuro.org/datasets/ds005047/derivatives/validation/code/Functional-localizer-experiment-analysis/s4a_analysis_floc.m**


## Validation
### Basic quality control
The fundamental quality control suggests that the data displays good quality. To evaluate the quality of the structural data obtained from NFED, we employed four crucial metrics:Coefficient of Joint Variation(CJV), Contrast-to-Noise Ratio (CNR), Signal-to-Noise Ratio in Grey Matter (SNR_GM), and Signal-to-Noise Ratio in White Matter (SNR_WM). Specifically, the CJV is between white matter(WM) and grey matter(GM).The CNR assesses the relationship between the contrast of GM and WM with the noise present in the image. The SNR assesses the connection between the mean signal measurements and the noise present in the image. The SNR assessment is conducted individually for GM and WM.For quality control of the NFED’s functional scans, we assessed the amount of head motion for each participant and the temporal signal-to-noise ratio (tSNR) of the time-series data, separately.
**code: https://openneuro.org/datasets/ds005047/derivatives/validation/code/validation/T1_Image-quality-metrics/T1data/noiseeval/cal_SNRindex.m**
**code: https://openneuro.org/datasets/ds005047/derivatives/validation/code/validation/FD/FD.py**
**code: https://openneuro.org/datasets/ds005047/derivatives/validation/code/validation/tSNR/tSNR.py**
### The visual cortex exhibits reliable BOLD responses to natural facial expression videos stimuli in main experiment.
In the main experiment, there were 5 participants who accomplished 10 sessions, each consisting of of 4 training runs and 2 test runs, making a total of 60 runs(40 training and 20 test runs) in all. For each run, 30 (videos)*2 (repetitions) x 3 (seconds) betas were acquired. Z-score normalization was performed on the raw betas at each voxel for each run.The betas were then averaged across stimulus repetitions to generate a vector of betas. In general, 44 runs (40 training runs and 4 test runs)*90 betas were acquired. Hence, the test-retest reliability of responses to these videos in main experiment were evaluated through computing the Pearson correlation between the 90 betas obtained from the even runs and odd runs on each vertex.
**code:https://openneuro.org/datasets/ds005047/derivatives/validation/code/validation/reliability_face/reliability_face.py**
### noise celling
The code are available at  https://openneuro.org/datasets/ds005047/validation/code/noise_celling/sub-xx" store the intermediate files required for running the program
**code: https://openneuro.org/datasets/ds005047/derivatives/validation/code/noise_celling/Noise_Ceiling.py**

### Correspondence between human brain and DCNN
The code are available at  https://openneuro.org/datasets/ds005047. We combined the data from main experiment and functional localizer experiments to build an encoding model to replicate the hierarchical correspondences of representation between the brain and the DCNN. The encoding models were built to map artificial representations from each layer of the pre-trained VideoMAEv2 to neural representations from each area of the human visual cortex as defined in the multimodal parcellation atlas.
**code: https://openneuro.org/datasets/ds005047/derivatives/validation/code/dnnbrain/**

### Semantic metadata of action and expression labels reveal that NFED can encode temporal and spatial stimuli features in the brain
The code are available at  https://openneuro.org/datasets/ds005047/validation/code/semantic_metadata/xx_xx_semantic_metadata" store the intermediate files required for running the program.
**code:https://openneuro.org/datasets/ds005047/derivatives/validation/code/semantic_metadata/**

## results
 The results can be viewed at  "https://openneuro.org/datasets/ds005047/derivatives/validation/results/brain_map_individual".

## Whole-brain mapping
The whole-brain data mapped to the cerebral cortex as obtained from the technical validation.
**code:https://openneuro.org/datasets/ds005047/derivatives/validation/results/show_results_allbrain/Showresults.m**

## Mannually prepared environment
We provide the *requirements.txt* to install python packages used in these codes. However, some packages like *GLM* and *pre-processing* require external dependecies and we have provided the packages in the corresponding file.

## stimuli
The video stimuli used in the NFED experiment are saved in the "https://openneuro.org/datasets/ds005047/stimuli" folders.


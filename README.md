# sleeping_pigeon_fMRI
Guide for preprocessing rsfMRI data and quality check.
All scripts are written in jupyter-notebook.
To run the codes, you need to install FSL, AFNI, and ANTs.
These codes will apply following preprocessing steps:
1) converting dcm 2 nifti format (dcm2nii function) and upscale voxel size by factor of 10 (3drefit function). 
2) Frame-Wise displacement (using fsl_motion_outliers function): This measure indexes the movement of the head from one volume to the next, and is calculated as the sum of the absolute values of the differentiated realignment estimates (by backwards differences) at every timepoint.
3) Generating a population-based template using antsMultivariateTemplateConstruction2.sh script
4) Normalizing the functional data to population based template: Non-Linear registration of anatomical image to the standard space (fnirt function), linear registration of functional images to anatomical space (flirt function), concatenate both steps to directly normalizing functional data into the standard space.
5) Motion correction (mcflirt function): This code will generate 6 estimated motion parameters (3 translation + 3 rotation).
6) Slice time correction (slicetimer function): Unlike a photograph, in which the entire picture is taken in a single moment, an fMRI volume is acquired in slices. Each of these slices takes time to acquire - from tens to hundreds of milliseconds.The slice-timing correction can lead to significant increases in statistical power for studies with longer TRs (e.g., 2s or longer).
7) Spatial smoothing (3dBlurInMask function): This script smooths the ts with a gaussian kernel of 700um (~2 times voxel size)
8) Intensity normalization:This script normalizes runs global intensity normalization with grand mean = 10000 across for group analysis.
9) temporal filtering (3dTproject function): High pass filters cut off frequencies below a certain threshold which of course should below the lowest frequency of interest. Since in fMRI, noise is disproportionally expressed in low frequencies, high-pass filtering can also help whitening the noise (i.e. flattening the noise spectrum), which helps fulfilling GLM assumptions.
10) removing dummy scans (fslroi function): This is a product of the machine’s magnetic field first lining up the protons in the water molecules in the brain, before they have gone into a stead stat. To remove the thermal noise effect we remove first 5 volumes. In addition, to avoide the edge effect of temporal filtering, we discarded the last 5 volumes.
11) Temporal signal-to-noise ratio (tSNR): A useful measure of image time course stability is the Temporal Signal-to-Noise Ratio (TSNR) calculated by dividing the mean of a time series by its standard deviation.
12)  Physiological Noise Modelling: we used Pnm in FSL to find the voxel-wise regresorse for respiration signal to model in GLM
13) GLM analysis
14)  Regresing out the Voxel wise EVs (from Pnm, film_gls function) and motion parameters (fsl_regfilt).

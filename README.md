# Resting-state functional magnetic resonance imaging
rs-fMRI is a repository of code for preprocessing, denoising, and running quality control on resting-state fMRI datasets, using Matlab.

Resting-state fMRI is highly sensitive to artefacts caused by in-scanner movement. These artefacts cause spurious correlations in the timeseries data that hinder functional connectivity analyses. This code applies a wide range of popular data denoising methods designed to correct for these artefacts and produces a range of quality control benchmarks in order to faciliate the evaluation of their efficacy.

This code is broken into four main subdirectories, **prepro**, **qc**, **stats**, and **func**. 
The details of each subdirectory can be found below.

## prepro

Code in the ***prepro*** subdirectory was developed and tested on the Multi-modal Australian ScienceS Imaging and Visualisation Environment (MASSIVE: https://www.massive.org.au).
This code uses the following software:
- MATLAB R2014a (8.3.0.532) 64-bit (glnxa64) February 11, 2014
- SPM8 r5236
- REST 1.8
- FSL 5.0.9
- ANTs 1.9.v4
- ICA-AROMA

These scripts are hard coded for use with my own projects but can be edited to run on new data.

The preprocessing pipeline is broken into three main scripts
- prepro_base.m
- prepro_noise.m
- prepro_extractTS_FSL.m

All three of these are called by the script
- run_prepro.m

run_prepro.m is the script that the user should edit.

run_prepro.m allows the user to select from a handful of different noise correction strategies but each of these methods works off of the output file (except see ICA-AROMA) of the prepro_base script. The decision to split the scripts was so that users may run all the noise correction options available on their dataset without unnecessarily repeating the base processing steps. This is desirable because it allows the user to examine how each noise correction strategy compares to the others (see the **qc**).

The following is a brief summary of what the two scripts do

#### prepro_base.m
	1 - Segment T1 using SPM (Generate tissue masks)
	2 - Discard first 4 volumes (optional. set using input argument)
	3 - Slice-timing correction (optional. set using input argument)
	4 - Despiking (optional. set using input argument)
	5 - EPI Realignment
	6 - Co-registration of realigned images to native T1
	7 - Spatially normalize T1 to MNI template
	8 - Application of T1-spatial normalization parameters to coregistered EPI and tissue masks
	9 - Mask out non-brain voxels
	10 - Linear detrending of realigned EPI time series (uses REST) (optional. set using input argument)
	11 - Modal intensity normalisation (to 1000) (optional. set using input argument)
	12 - Spatial smoothing (for ICA-AROMA)

#### prepro_noise.m
	1 - Correct noise in EPI output from step 11 of prepro_base script (or output from step 12 in case of ICA-AROMA)
	2 - Bandpass filter (includes demeaning)
	3 - Spatial smoothing (not in case of ICA-AROMA)	
	
#### Basic usage (Matlab)
	>> WhichMASSIVE = 'M2';
	>> WhichProject = 'UCLA';
	>> WhichSessScan = 'Sess1_Scan1';
	>> subject = '1008.2.48.009'; % <-- string containing subject ID
	>> run_prepro(WhichMASSIVE,WhichProject,WhichSessScan,subject)

#### Usage (MASSIVE)
- MASSIVE_Job.sh / MASSIVE_Job_M3.sh

This script submits an array slurm job for processing multiple participants at once on M2/M3

## qc

The code in the **qc** subdirectory can be used to reproduce the figures in **An evaluation of the efficacy, reliability, and sensitivity of motion correction strategies for resting-state functional MRI.** L. Parkes, B. D. Fulcher, M. Yucel, & A. Fornito. *bioRxiv* (2017).

See Figshare (https://doi.org/10.4225/03/595193482c03e) for fully processed rs-fMRI timeseries data for two of the three datasets used in the above pre-print.

#### QC.m

This script computes the quality control benchmarks outlined in the above pre-print and produces the corresponding figures.
To reproduce the figures, the user will need to download the processed data from Figshare (see above) and edit the directories accordingly (see % Set project variables).

Example:

Assuming a user has downloaded an unzipped all the contents of the Figshare to their desktop (~/Desktop/), the following snippet should allow the user to reproduce the figures.

	% ------------------------------------------------------------------------------
	% Set project variables
	% ------------------------------------------------------------------------------
	switch WhichProject
		case 'UCLA'
			projdir = '~/Desktop/UCLA/';
			sublist = [projdir,'UCLA.csv'];
			datadir = [projdir,'data/'];
			preprostr = '/rfMRI/prepro/';

			TR = 2;

			nbsdir = '~/Desktop/UCLA/NBS_tDOF_spikeReg/';
		case 'NYU_2'
			projdir = '~/Desktop/NYU_2/';
			sublist = [projdir,'NYU_2.csv'];
			datadir = [projdir,'data/'];
			preprostr = '/session_1/rest_1/prepro/';
		
			TR = 2;
	end

#### QC_ThePlot.m

This script will produce a *carpet plot* (see Power 2016, NeuroImage) so that the user can perform subject-level quality control.
This script is also hard coded for use with my own projects but can be edited to run on new data.

## stats

The **stats** subdirectory contains the code used to compute whole-brain differences in functional connectivity using the Network Based Statistic (Zalesky et al., 2014. NeuroImage)

## func

The **func** subdirectory contains matlab functions and shell scripts needed for the repo

antsRegistrationSyN.sh and antsRegistrationSyNQuick.sh are copyright (c) 2009-2013 ConsortiumOfANTS. All rights reserved.

# Publications
See the following publications for examples of this code in use:
- **An evaluation of the efficacy, reliability, and sensitivity of motion correction strategies for resting-state functional MRI.** L. Parkes, B. D. Fulcher, M. Yucel, & A. Fornito. *bioRxiv* (2017).

Linden Parkes, Brain & Mental Health Laboratory, Monash University
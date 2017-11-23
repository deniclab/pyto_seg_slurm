# -= pyto_seg_slurm =-
Submission scripts for image pre-processing, [pyto_segmenter](https://github.com/deniclab/pyto_segmenter/) segmentation, and post-segmentation analysis on Harvard's Odyssey cluster

Author: Nicholas Weir, nweir[at]fas[dot]harvard[dot]edu

__Note:__ These scripts are currently designed for running on my personal RC account, and will require some fiddling with paths and other preparation to make it work for you. Ask me if you need help.

## Contents:
Three sets of scripts contained in separate sub-directories:

__preprocessing:__ For pre-segmentation processing of images acquired on the Murray/Garner lab spinning disk confocal.
- image_preprocessing.sh, img_file_cleanup.py, and batch_merge_channels.py: for automatically renaming image filenames based on stage position numbering and a stage_positions.STG reference table, then generating ImageJ/Fiji-format multi-channel images and maximum intensity projections.
- norm_camera.sh and .py: For normalizing camera background both within and across images according to a camera background channel acquired at each stage position/timepoint combination.

__segmentation:__ For performing segmentation on fluorescence microscopy images using pyto_segmenter. See the scripts for command-line arguments (more details will be explained in this readme soon)
- batch_pex_seg.sh, batch_pex_seg_cfp.sh, and batch_pex_seg.py: for segmenting foci visualized in either the red (batch_pex_seg.sh) or cyan (batch_pex_seg_cfp.sh) channels.
- batch_mito_seg.sh and .py: For segmenting reticular structures visualized in the cyan channel. _important note_: this script is optimized specifically for segmenting mitochondria using endogenously expressed Tom70-mTurquoise2 imaged at 35% laser power with a 100 ms exposure and 100 units EM gain. For other purposes, it will need to be modified.
- mito_and_pex_seg.sh and .py, mito_and_pex_seg_nooverlap.sh and .py: For segmenting both foci (red) and reticular structures (cyan). Same note as above for batch_mito_seg. nooverlap removes parts of reticular structures that overlap with foci (but not vice versa).

__analysis__: For post-segmentation analysis of segmented structures.
- parallel_analysis.sh and .py: for measuring fluorescence intensities in the yellow channel for objects segmented in either cyan or red above. outputs a .csv-formatted table of objects with intensity and volume for each object along with origin metadata.
- combine_outputs.sh and .py: for combining multiple .csv-formatted outputs generated using parallel_analysis.sh and .py.

## Running analysis on the Odyssey cluster

Before you can run analysis on the cluster, you'll need to set up an environment using Python 3 (all of our segmentation and analysis scripts are implemented in Python 3). You can find general instructions for getting this up and running [here](https://www.rc.fas.harvard.edu/resources/documentation/software-on-odyssey/python/). Specifically, you'll need to do the following:

1. log in to the cluster
2. `cd ~`  
to navigate to your home folder
3. `module load Anaconda3/2.1.0-fasrc01`  
to switch to Python 3 (with most packages - numpy, scipy, scikit-image, matplotlib, pandas, etc. - already implemented)
4. `conda create -n PYTO_SEG_ENV --clone="$PYTHON_HOME"`  
This will create a new "python environment" called PYTO_SEG_ENV. We need to do this because we need to update a few python packages beyond what's currently installed on the cluster.
5. `source activate PYTO_SEG_ENV`  
This will activate the new python environment you just made.
6. `conda install pandas`
to update to the newest version of pandas. __important__: This must be done before step 7, or else you will have to repeat step 7 after completing this installation.
7. `conda install numpy`  
to update to the newest version of numpy (the one installed here is a couple of updates behind, and I had to use functions from a newer version of python)
8. `conda install scikit-image`  
to update to the newest version of scikit-image, which has a bunch of image file loading and saving functions that we use. The old version on the cluster can't handle some of the file formats we use. This will take a while because it needs to update a whole bunch of other packages.
9. Set up your account so that it automatically activates your python environment every time you log into the cluster. This way you don't need to do it every time you log in before running our scripts.
    1. If you're not already there, switch to your home folder:  
    `cd ~`
    2. Open up your .bash_profile file, which tells the cluster what to do upon startup:  
    `vim .bash_profile`  
    __note:__ _You're going to be making these changes using a text editor called Vim. Because you can't use programs with a GUI (e.g. Atom, RStudio, MatLab, etc.) on the cluster, you'll want to get used to using Vim to make small edits to scripts. It's un-intuitive but great once you familiarize yourself. See http://vim.wikia.com/wiki/Tutorial for a tutorial._
    3. Navigate to the end of the file and add the following two lines:  
    `module load Anaconda3/2.1.0-fasrc01`  
    `source activate PYTO_SEG_ENV`  
    4. Save the file (press Esc to exit insert mode, then type :wq and hit Enter)


Last updated: 10.25.2017

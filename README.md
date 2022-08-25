LivingPark is a collection of Jupyter notebooks that reproduce published
MRI measures of Parkinson's Disease. It also contains tools to work with
data from the Parkinson's Progression Markers Initiative
([PPMI](https://www.ppmi-info.org/)). A (very) brief introduction to
LivingPark is available
[here](https://docs.google.com/presentation/d/1PqyRLhB9PoqW2UCnvVX8CuqEW2TfPiECQlMEheeiorg/edit#slide=id.g12ed72e6175_0_106).

# How to organize your Jupyter notebook

Here is a typical outline for the notebook:

* Introduction
  - Reference to the paper to be reproduced
  - Cohort table to be reproduced
  - Brief summary of image analysis to be reproduced
  - Key results to be reproduced
  - Call `livingpark_utils.utils.prologue()`

* Cohort definition
  - PPMI Study Data download. Use `livingpark_utils.utils.install_ppmi_study_files`
  - Cohort matching

* Image pre-processing
  - Download DataLad dataset associated with the paper (stored at the BIC). Use `livingpark_utils.utils.install_datalad_repo`.
  - Check that subjects in reproduced cohort are available
  - Download missing subjects from PPMI
  - Convert missing subjects to BIDS using Heudiconv
  - Commit new BIDS subjects to DataLad dataset, push to BIC
  - Run containerized (DataLad or Boutiques) pre-processing pipeline for required subjects
  - Commit pre-processed data to dataset, push back to BIC

* Analysis

# General recommandations to write Jupyter notebooks

* DO "Restart and Run all" your notebook before committing it.

* DO include a `requirements.txt` file containing the Python packages required by the notebook.

* DO run [Black](https://black.readthedocs.io/en/stable/) on the notebook before committing.


# How to write Jupyter Notebooks that use PPMI data

We are currently working on reproducing MRI measures using the PPMI dataset. 
The main contributions are expected to be in the form of Jupyter Notebooks
using PPMI data. However, PPMI's Data Usage Agreement prevents us from sharing
patient ids or any other individual-level data. Therefore, notebooks should 
follow the following practices:

* DO structure the Git repository of your notebook using the [YODA](https://handbook.datalad.org/en/latest/basics/101-127-yoda.html) principles.

* DON'T include any PPMI data or metadata with your notebook. Instead, your notebook 
should download data from the PPMI website directly. Use the [PPMI downloader](https://github.com/LivingPark-MRI/ppmi-scraper) to help with this
step. 

* DON'T commit your PPMI login or password with your notebook. The [PPMI downloader](https://github.com/LivingPark-MRI/ppmi-scraper)
will store your login and password in a file (default: `.ppmi_config`), don't commit this file.

* DON'T display individual-level data or patient ids in your notebook.
If you need to display data, make sure to represent aggregate measures
such as histograms or counts. Check Pandas' `hist` and `group_by` functions!

* DON'T use "magic files" that can't be retrieved from the PPMI database or 
created by another notebook. If you do so, nobody will be able to run your notebook.

* DO save important files produced by your notebook, so that other notebooks could start from them 
  after running your notebook.
  
* DO fix random seeds when making random selections and sort the DataFrames and list to make your notebook reproducible.

* DO include as much information as possible about what your notebook does. Use Markdown cells to explain what the notebook does in lay terms. Use Python 
comments to explain what the Python code does.

* DO include as many `assert` statetements as necessary. Your notebook will be run by other users and you want to make sure that 
the assumption it makes hold. Also include sanity checks whenever needed, for instance to check that a subject isn't included multiple times in a cohort.

* DO look at other examples of LivingPark notebooks. Current complete examples:
  - [![scherfler-etal-2011](https://img.shields.io/badge/view%20on-nbviewer-brightgreen.svg)](https://nbviewer.org/github/LivingPark-MRI/scherfler-etal/blob/main/scherfler-etal.ipynb)


# How to build a PPMI cohort

The first step to reproduce a published paper is to reproduce a cohort with
similar clinical, behavioral and demographics variables. PPMI contains a
number of metadata files to retrieve these variables, accessible from the
["Study Data"](https://ida.loni.usc.edu/pages/access/studyData.jsp) page.
This page also includes a Data Dictionary and a Code List to help interpreting 
the variables. 

LivingPark's [PPMI downloader](https://github.com/LivingPark-MRI/ppmi-scraper)
will allow your notebooks to download these metadata files.

LivingPark also contains notebooks to clean variables (remove mistakes,
impute missing data, etc), which produce the following files:
| Filename | Produced by | Contains |
|----------|-------------|----------|
| `MRI_info.csv` | [ppmi-MRI-metadata](https://github.com/LivingPark-MRI/ppmi-MRI-metadata) | 3D T1-weighted images by visit |
| `MDS_UPDRS_Part_III_clean.csv` | [ppmi-treatment-and-on-off-status](https://github.com/LivingPark-MRI/ppmi-treatment-and-on-off-status) | Cleaned-up PDSTATE and PDTRTMNT|

Your cohort-building notebooks should start from these files rather than redoing similar cleanups.

# How to launch computations (pre-processing or statistics)

All computations need to be containerized and properly documented. Three computation modes are envisaged:
1. Computation is launched from the notebook, executed on the localhost using Boutiques' Python API. Example: https://github.com/LivingPark-MRI/scherfler-etal
2. Same as 1 but computation is launched on a SLURM cluster reachable from the notebook, using the %slurm magic command.
3. Computation is externalized to BIC, notebook downloads results (for now, from BIC, in the future, from PPMI) and documents how they were obtained using mr_proc. 
   
Mode 1 and 2 are more appropriate for group analyses, light computations, and computations that are specific to a particular paper. Mode 3 is more appropriate for heavy computations such as Freesurfer pre-processing.

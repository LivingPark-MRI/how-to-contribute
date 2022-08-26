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
  - Call `LivingParkUtils.notebook_init()`

* Cohort Preparation
  - PPMI Study Data download. Use `LivingParkUtils.install_ppmi_study_files(...)`
  - Cohort matching. Pandas is your friend :)

* Image Pre-Processing
  - [Reach out](mailto:livingpark@lists.concordia.ca) to PPMI data managers at the BIC.
  - **IF** pre-processing results are available for your analysis, download them from the BIC server using sftp. Make sure to explain in the notebook how these results were obtained and link to the container image used to produce them. In the future we will host this data on the PPMI servers.
  - **ELSE**:
    - Download missing imaging data from PPMI. Use `LivingParkUtils.download_missing_nifti_files(...)`
    - Run containerized (Boutiques or DataLad) pre-processing pipeline for the required subjects. If computation is too heavy, use [SLURM magic commands](https://github.com/NERSC/slurm-magic) to offload it to a SLURM cluster.

* Quality Control: make sure to implement at least some basic quality control for the pre-processed data.

* Statistical Analysis
   - Run containerized (Boutiques or DataLad) statistical analysis pipelines

# General recommandations to write Jupyter notebooks

* DO "Restart and Run all" your notebook before committing it.

* DO include a `requirements.txt` file containing the Python packages required by the notebook.

* DO run [Black](https://black.readthedocs.io/en/stable/) on the notebook before committing.


# How to write Jupyter Notebooks that use PPMI data

The PPMI Data Usage Agreement prevents us from sharing
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

[`LivingParkUtils.install_ppmi_study_files(...)`](https://github.com/LivingPark-MRI/livingpark-utils)
will allow your notebooks to download these metadata files.

LivingPark also contains notebooks to clean variables (remove mistakes,
impute missing data, etc), which produce the following files:
| Filename | Produced by | Contains |
|----------|-------------|----------|
| `MRI_info.csv` | `from livingpark_utils.scripts import mri_data` | 3D T1-weighted images by visit |
| `MDS_UPDRS_Part_III_clean.csv` | `from livingpark_utils.scripts import pd_status` | Cleaned-up PDSTATE and PDTRTMNT|

Your cohort-building notebooks should start from these files rather than redoing similar cleanups.

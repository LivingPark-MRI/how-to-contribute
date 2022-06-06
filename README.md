LivingPark is a collection of Jupyter notebooks that reproduce published
MRI measures of Parkinson's Disease. It also contains tools to work with
data from the Parkinson's Progression Markers Initiative
([PPMI](https://www.ppmi-info.org/)). A (very) brief introduction to
LivingPark is available
[here](https://docs.google.com/presentation/d/1PqyRLhB9PoqW2UCnvVX8CuqEW2TfPiECQlMEheeiorg/edit#slide=id.g12ed72e6175_0_106).

# How to contribute

We are currently working on reproducing MRI measures using the PPMI dataset. 
The main contributions are expected to be in the form of Jupyter Notebooks
using PPMI data. However, PPMI's Data Usage Agreement prevents us from sharing
patient ids or any other individual-level data. Therefore, notebooks should 
follow the following practices:


* DON'T include any PPMI data or metadata with your notebook. Instead, your notebook 
should download data from the PPMI website directly. We are developping a [Python
API](https://github.com/LivingPark-MRI/ppmi-scraper) to help with this
step. Be careful not to commit your PPMI login/password!

* DON'T display individual-level data or patient ids in your notebook.
If you need to display data, make sure to represent aggregate measures
such as histograms or counts. Check Pandas' `hist` and `group_by` functions!

* DON'T use "magic files" that can't be retrieved from the PPMI database or 
created by another notebook. If you do so, nobody will be able to run your notebook.

* DON'T commit your PPMI login or password with your notebook. We'll soon update the API 
to prevent this. 

* DO "Restart and Run all" your notebook before committing it.

* DO save important files produced by your notebook, so that other notebooks could start from them 
  after running your notebook.

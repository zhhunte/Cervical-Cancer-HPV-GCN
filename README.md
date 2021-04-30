# Cervical-Cancer-HPV-GCN
This workflow attempted to analyze SRA data related to cervical organoids infected with human papillomavirus through GEM construction, preprocessing, and subsequent Gene Co-expression Network analysis. (Keyword is attempt) 
## Lab Overview
Human Papillomavirus (HPV) is the most commonly sexually transmitted infection in the United States and endemic worldwide. However, even more troubling is the instigation of the virus in many different forms of cancer. In 2017, it was reported that HPV-related cancers were globally responsible for 8.6% of all cancer cases per year in women and 0.8% of all cases per year in men. One of the most prolific types of HPV-related cancers is observed in the cervix, making up the vast majority of incidenct cancer cases (https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5520228/). A Gene Co-expression Network (GCN) of cervical cancer tissue with a HPV infection could reveal malfunctioning genes whose incorrect expression may be due to the viral infection and allow for tumor development. 
## Lab Objectives
To conduct this analysis, 
* Create a GEM using SRA data and GEMmaker.
* Preprocess the GEM through log2 and quantile normalization.
* Construct a GCN from the processed GEM.
## Lab Tasks
### Task A: Making the GEM
1. *Clone GEMmaker*. 

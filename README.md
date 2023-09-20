# Global Genes RareX Challenge 2023: Identify underrecognized symptoms / Expand definition of rare disease

## Overview
Global Genes announces our team ‘Best open source method to benefit rare disease research’ as one of the winners for this challenge. 
<br><br>
https://t.co/lRXHjFD9w8 <br>
https://twitter.com/RARE_X_/status/1704538663445471713?s=20

In this task, Global Genes RareX Challenge committee provided with de-identified data (not downloadable) on patient populations in a flat table highlighting different phenotypes and genetic diagnoses. The task focused on expanding known phenotypes with previously unrecognized symptoms. We explore this data to determine if there are novel or underrecognized phenotypes correlated with specific rare diseases and submitted our results, approach and code. 

## Mehtod
### Clean and filter data
- Ignore cases has no one value
- Impose a minumum number of cases
### Term Frequency(TF) and Inverse Document Frequency(IDF) information (tf/idf)
- term frequnecy: number of the times, a term is mentioned in a disease / total number of the terms in a disease
- Inverse Document Frequency(IDF): IDF(h) = (total diseases in the dataset) / (number of the diseases,a term, the term (h) is mentioned +1) + 1
### Generate computational data
- Compute the specification of terms for diseases with tf/idf
  - compute term frequency (tf) for each term/symtoms with diseases of survey dataset
  - compute survey idf of the terms with diseases from survey dataset
  - compute hpo idf of the terms with diseases from hpo dataset
  - compute tf/idf by multiplying combine hpo and survey idf with term frequency of survet dataset 
- Compute the freuency of a term in survey dataset as orphanet with the following classifications.
  - Very frequent: more than 80%
  - Frequent: between 30% and 80%
  - Occasional: fewer than 30%
### Compare RARE-X Data to Orphanet and HPO
- Check new hpo in the survey: Look up the new hpo in the survey data which are not in the orpahnet/hpo dataset
- Look up best similar hpo term from orphanet/hpo dataset for a term if the term is not found in the the orpahnet/hpo dataset
- Check novel hpo if it is new and not simialir to any term in hpo/orphanet dataset.
### Generate data for the analyze
We generate different columns with binary value (0 ,1) to analysis the results for each condition below. We fill the data of a column to 1 if the condition met below, otherwise 0.
- Top terms: find top top terms if it is above the mean value of the survey_hpo_tf_idf
- NewRareX: new terms from top terms list
- novel terms: frequent and new terms from top terms list which is not matched/similar to existing terms in hpo/orpha
We also added two columns 'Fisher_pvalue' and 'matched_terms_count' to test the results:
- Fisher_pvalue: this column defines pvalue of the fisher exact test for a term. We calculate the pvalue for the count of a term in the survey dataset and hpo dataset.
- matched_terms_count: this column defines number of the times a matched/similar term from the best_match_terms_pyhpo_gt_06 have been mentioned with the disease.

# Global Genes RareX Challenge 2023: Identify underrecognized symptoms / Expand definition of rare disease

## Overview
Our team was one of the winners for Task 1 of the [Global Genes RareX Challenge committee (GGRC)](https://globalgenes.org/)!. 

"Best open source method to benefit rare disease research": Chong Lab team, led by Jessica Chong, Ph.D., Assistant Professor in Pediatrics at the University of Washington, Seattle. Code is provided here Task1_RareX.ipynb

https://t.co/lRXHjFD9w8 <br>
https://twitter.com/RARE_X_/status/1704538663445471713?s=20


In Task 1, the GGRC provided de-identified, individual-level phenotypic data associated with a named diagnosis in a flat table. We cannot share this data. The task focused on identifying differences between the known phenotypic effects associated with a specific genetic diagnosis and phenotypic effects reported by families with that diagnosis in order to identify previously under-recognized symptoms. We explored this data to determine if there are novel or underrecognized clinical findings for each rare disease.

We needed to map each symptom label in the GGRC dataset to an HPO term in order to take advantage of the HPO/OMIM/OrphaNet database. We used multiple methods (NLP as well as tree-based similarity) to identify potentially overlapping symptoms/terms between GGRC and HPO. These terms were manually reviewed to confirm/refute potential overlap. We also reviewed the output of these scripts manually to identify the differences that were most likely to be clinically meaningful and therefore of interest to GGRC. 

## Mehtod
### Clean and filter data
- Ignore cases has no one value
- Impose a minumum number of cases
### Term Frequency(TF) and Inverse Document Frequency(IDF) information (tf/idf)
- Term frequnecy: number of the times, a term is mentioned in a disease / total number of the terms in a disease
- Inverse Document Frequency(IDF): IDF(h) = (total diseases in the dataset) / (number of the diseases, a term (h) is mentioned +1) + 1
### Generate computational data
- Compute the specification of terms for diseases with tf/idf
  - Compute term frequency (tf) for each term/symtoms with diseases of survey dataset
  - Compute survey idf of the terms with diseases from survey dataset
  - Compute hpo idf of the terms with diseases from hpo dataset
  - Compute tf/idf by multiplying combine hpo and survey idf with term frequency of survet dataset 
- Compute the frequency of a term in survey dataset as orphanet with the following classifications.
  - Very frequent: more than 80%
  - Frequent: between 30% and 80%
  - Occasional: fewer than 30%
- Map the symptoms to HPO terms
### Compare RARE-X Data to Orphanet and HPO
- Check new hpo in the survey: Look up the new hpo in the survey data which are not in the orpahnet/hpo dataset
- Look up best similar hpo term from orphanet/hpo dataset for a term if the term is not found in the the orpahnet/hpo dataset
  - We used pyhpo package (https://pypi.org/project/pyhpo/) to find match between terms  
- Check novel hpo if it is new and not simialir to any term in hpo/orphanet dataset.
### Generate data for the analyze
We generate different columns with binary value (0 ,1) to analysis the results for each condition below. We fill the data of a column to 1 if the condition met below, otherwise 0.
- Top terms: find top top terms if it is above the mean value of the survey_hpo_tf_idf
- NewRareX: new terms from top terms list
- top_terms_not_match_but_frequent (novel terms): frequent and new terms from top terms list which is not matched/similar to existing terms in hpo/orpha
- matched_terms_frequent_both: a term is common/frequent to both dataset survey and hpo dataset
- top_matched_terms_frequent_survey_only: an existing term which is not frequent in orpha/hpo dataset but frequent in survey, and also in the top term list of survey_hpo_tf_idf.
- top_tf_idf_diff: new top terms if the value of abs_tf_idf_difference is above its mean value
- top_hpo_term_prop_diff: new top terms if the value of abs_hpo_term_prop_difference is above its mean value
We also added two columns 'Fisher_pvalue' and 'matched_terms_count' to test the results:
- Fisher_pvalue: this column defines pvalue of the fisher exact test for a term. We calculate the pvalue for the count of a term in the survey dataset and hpo dataset.
- matched_terms_count: this column defines number of the times a matched/similar term from the best_match_terms_pyhpo_gt_06 have been mentioned with the disease.

## Example
We show some examples to find the novel term, new terms, and common terms for a disease (Kleefstra syndrome ):

```python
disease = rx_orphanet_summary.loc[rx_orphanet_summary.index==rx_in_orphanet[0]]
```

Novel terms: The following new terms which are top terms for the Kleefstra syndrome show that abnormality of coordination, Somnambulism, Sleep onset insomnia, Drowsiness, Seizure, Abnormality of the musculature, Sleep apnea, .. are common. These terms are important terms for the Kleefstra syndrome but not exist/matched in the hpo/orpha dataset.
```python
disease[disease['top_terms_not_match_but_frequent']==1]
```
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Disease_Name</th>
      <th>Symptom</th>
      <th>Count</th>
      <th>term_proportion</th>
      <th>survey_tf_idf</th>
      <th>HPO_Code</th>
      <th>HPO_label</th>
      <th>survey_hpo_tf_idf</th>
      <th>Survey_OrphaClass</th>
      <th>HPO_Frequency_OrphaClass</th>
      <th>HPO_Count</th>
      <th>hpo_dataset_tf_idf</th>
      <th>Orpha_Frequency</th>
      <th>Orpha_Count</th>
      <th>orpha_dataset_tf_idf</th>
      <th>hpo_orpha_dataset_tf_idf</th>
      <th>Mapped_Disease_name</th>
      <th>Matched</th>
      <th>best_match_terms_pyhpo</th>
      <th>best_match_terms_pyhpo_gt_06</th>
      <th>abs_hpo_term_prop_difference</th>
      <th>fisher_value</th>
      <th>top_terms</th>
      <th>NewRareX</th>
      <th>top_hpo_term_prop_diff</th>
      <th>top_terms_not_match_but_frequent</th>
      <th>matched_terms_frequent_both</th>
      <th>top_matched_terms_frequent_survey_only</th>
      <th>Found_only_in_HPO</th>
      <th>matched_terms_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Kleefstra syndrome</td>
      <td>Coordination_Issues_Symptom_Present</td>
      <td>15.0</td>
      <td>0.625000</td>
      <td>0.022129</td>
      <td>HP:0011443</td>
      <td>Abnormality of coordination</td>
      <td>0.223117</td>
      <td>Frequent (79-30%)</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Kleefstra syndrome</td>
      <td>New in RareX</td>
      <td>HPO(0.44) Seizure: Frequent (79-30%)</td>
      <td>Not match</td>
      <td>NaN</td>
      <td>1.639086e-13</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>[]</td>
    </tr>
  </tbody>
</table>


New terms: The following terms are new and top terms for the Kleefstra syndrome. We see 'Abnormality of coordination', 'Somnambulism', 'Sleep onset insomnia', 'Drowsiness', .. from the follwing lists are new or different from hpo or orphanet dataset. 
```python
disease[disease['NewRareX']==1]
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Disease_Name</th>
      <th>Symptom</th>
      <th>Count</th>
      <th>term_proportion</th>
      <th>survey_tf_idf</th>
      <th>HPO_Code</th>
      <th>HPO_label</th>
      <th>survey_hpo_tf_idf</th>
      <th>Survey_OrphaClass</th>
      <th>HPO_Frequency_OrphaClass</th>
      <th>HPO_Count</th>
      <th>hpo_dataset_tf_idf</th>
      <th>Orpha_Frequency</th>
      <th>Orpha_Count</th>
      <th>orpha_dataset_tf_idf</th>
      <th>hpo_orpha_dataset_tf_idf</th>
      <th>Mapped_Disease_name</th>
      <th>Matched</th>
      <th>best_match_terms_pyhpo</th>
      <th>best_match_terms_pyhpo_gt_06</th>
      <th>abs_hpo_term_prop_difference</th>
      <th>fisher_value</th>
      <th>top_terms</th>
      <th>NewRareX</th>
      <th>top_hpo_term_prop_diff</th>
      <th>top_terms_not_match_but_frequent</th>
      <th>matched_terms_frequent_both</th>
      <th>top_matched_terms_frequent_survey_only</th>
      <th>Found_only_in_HPO</th>
      <th>matched_terms_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Kleefstra syndrome</td>
      <td>Coordination_Issues_Symptom_Present</td>
      <td>15.0</td>
      <td>0.625000</td>
      <td>0.022129</td>
      <td>HP:0011443</td>
      <td>Abnormality of coordination</td>
      <td>0.223117</td>
      <td>Frequent (79-30%)</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Kleefstra syndrome</td>
      <td>New in RareX</td>
      <td>HPO(0.44) Seizure: Frequent (79-30%)</td>
      <td>Not match</td>
      <td>NaN</td>
      <td>1.639086e-13</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>[]</td>
    </tr>
  </tbody>
</table>

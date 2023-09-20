# Global Genes RareX Challenge 2023: Identify underrecognized symptoms / Expand definition of rare disease

## Overview
Global Genes announces our team as one of the winners for this challenge. 

Best open source method to benefit rare disease research: Chong Lab team, led by Jessica Chong, Ph.D., Assistant Professor in Pediatrics at the University of Washington, Seattle. Code is provided here Task1_RareX.ipynb

https://t.co/lRXHjFD9w8 <br>
https://twitter.com/RARE_X_/status/1704538663445471713?s=20


In this task, Global Genes RareX Challenge committee provided with de-identified data (not downloadable) on patient populations in a flat table highlighting different phenotypes and genetic diagnoses. The task focused on expanding known phenotypes with previously unrecognized symptoms. We explore this data to determine if there are novel or underrecognized phenotypes correlated with specific rare diseases and submitted our results, approach and code. 

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
- top_terms_not_match_but_frequent (novel terms): frequent and new terms from top terms list which is not matched/similar to existing terms in hpo/orpha
- matched_terms_frequent_both: a term is common/frequent to both dataset survey and hpo dataset
- top_matched_terms_frequent_survey_only: an existing term which is not frequent in orpha/hpo dataset but frequent in survey, and also in the top term list of survey_hpo_tf_idf.
- top_tf_idf_diff: new top terms if the value of abs_tf_idf_difference is above its mean value
- top_hpo_term_prop_diff: new top terms if the value of abs_hpo_term_prop_difference is above its mean value
We also added two columns 'Fisher_pvalue' and 'matched_terms_count' to test the results:
- Fisher_pvalue: this column defines pvalue of the fisher exact test for a term. We calculate the pvalue for the count of a term in the survey dataset and hpo dataset.
- matched_terms_count: this column defines number of the times a matched/similar term from the best_match_terms_pyhpo_gt_06 have been mentioned with the disease.

## Example
We show some examples to find the novel term, new terms, common terms for a disease (Kleefstra syndrome ):

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
    <tr>
      <th>1</th>
      <td>Kleefstra syndrome</td>
      <td>CSHQ Subscale 5: Night Wakings</td>
      <td>11.0</td>
      <td>0.458333</td>
      <td>0.016228</td>
      <td>HP:0025236</td>
      <td>Somnambulism</td>
      <td>0.175368</td>
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
      <td>HPO(0.32) Sleep disturbance: Frequent (79-30%)</td>
      <td>Not match</td>
      <td>NaN</td>
      <td>1.476035e-09</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Kleefstra syndrome</td>
      <td>CSHQ Subscale 2: Sleep onset Delay</td>
      <td>12.0</td>
      <td>0.500000</td>
      <td>0.017703</td>
      <td>HP:0031354</td>
      <td>Sleep onset insomnia</td>
      <td>0.174368</td>
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
      <td>HPO(0.54) Sleep disturbance: Frequent (79-30%)</td>
      <td>Not match</td>
      <td>NaN</td>
      <td>1.698093e-10</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Kleefstra syndrome</td>
      <td>CSHQ Subscale 8: Daytime Sleepiness</td>
      <td>12.0</td>
      <td>0.500000</td>
      <td>0.017703</td>
      <td>HP:0002329</td>
      <td>Drowsiness</td>
      <td>0.139476</td>
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
      <td>HPO(0.36) Atypical behavior: Frequent (79-30%)</td>
      <td>Not match</td>
      <td>NaN</td>
      <td>1.698093e-10</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Kleefstra syndrome</td>
      <td>Abnormal_Muscle_Function_Symptom_Present</td>
      <td>11.0</td>
      <td>0.458333</td>
      <td>0.016228</td>
      <td>HP:0003011</td>
      <td>Abnormality of the musculature</td>
      <td>0.130043</td>
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
      <td>HPO(0.37) Hypotonia: Very frequent (99-80%)</td>
      <td>Not match</td>
      <td>NaN</td>
      <td>1.476035e-09</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Kleefstra syndrome</td>
      <td>CSHQ Subscale 7: Sleep Disordered Breathing</td>
      <td>11.0</td>
      <td>0.458333</td>
      <td>0.016228</td>
      <td>HP:0010535</td>
      <td>Sleep apnea</td>
      <td>0.119823</td>
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
      <td>HPO(0.41) Sleep disturbance: Frequent (79-30%)</td>
      <td>Not match</td>
      <td>NaN</td>
      <td>1.476035e-09</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Kleefstra syndrome</td>
      <td>Abnormal_Eye_Movement_Symptom_Present</td>
      <td>11.0</td>
      <td>0.458333</td>
      <td>0.017703</td>
      <td>HP:0000496</td>
      <td>Abnormality of eye movement</td>
      <td>0.115640</td>
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
      <td>HPO(0.16) Hypertelorism: Very frequent (99-80%)</td>
      <td>Not match</td>
      <td>NaN</td>
      <td>1.476035e-09</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Kleefstra syndrome</td>
      <td>Abnormal_Ear_Symptom_Present</td>
      <td>8.0</td>
      <td>0.333333</td>
      <td>0.017596</td>
      <td>HP:0000598</td>
      <td>Abnormality of the ear</td>
      <td>0.109347</td>
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
      <td>HPO(0.26) Hearing impairment: Occasional (29-5%)</td>
      <td>Not match</td>
      <td>NaN</td>
      <td>6.680639e-07</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Kleefstra syndrome</td>
      <td>Conductive_Hearing_Loss_Symptom_Present</td>
      <td>10.0</td>
      <td>0.416667</td>
      <td>0.023058</td>
      <td>HP:0000405</td>
      <td>Conductive hearing impairment</td>
      <td>0.099830</td>
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
      <td>HPO(0.45) Hearing impairment: Occasional (29-5%)</td>
      <td>Not match</td>
      <td>NaN</td>
      <td>1.201914e-08</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Kleefstra syndrome</td>
      <td>Abnormal_EEG_Symptom_Present</td>
      <td>8.0</td>
      <td>0.333333</td>
      <td>0.012875</td>
      <td>HP:0002353</td>
      <td>EEG abnormality</td>
      <td>0.073009</td>
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
      <td>HPO(0.17) Seizure: Frequent (79-30%)</td>
      <td>Not match</td>
      <td>NaN</td>
      <td>6.680639e-07</td>
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
    <tr>
      <th>1</th>
      <td>Kleefstra syndrome</td>
      <td>CSHQ Subscale 5: Night Wakings</td>
      <td>11.0</td>
      <td>0.458333</td>
      <td>0.016228</td>
      <td>HP:0025236</td>
      <td>Somnambulism</td>
      <td>0.175368</td>
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
      <td>HPO(0.32) Sleep disturbance: Frequent (79-30%)</td>
      <td>Not match</td>
      <td>NaN</td>
      <td>1.476035e-09</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Kleefstra syndrome</td>
      <td>CSHQ Subscale 2: Sleep onset Delay</td>
      <td>12.0</td>
      <td>0.500000</td>
      <td>0.017703</td>
      <td>HP:0031354</td>
      <td>Sleep onset insomnia</td>
      <td>0.174368</td>
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
      <td>HPO(0.54) Sleep disturbance: Frequent (79-30%)</td>
      <td>Not match</td>
      <td>NaN</td>
      <td>1.698093e-10</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Kleefstra syndrome</td>
      <td>CSHQ Subscale 8: Daytime Sleepiness</td>
      <td>12.0</td>
      <td>0.500000</td>
      <td>0.017703</td>
      <td>HP:0002329</td>
      <td>Drowsiness</td>
      <td>0.139476</td>
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
      <td>HPO(0.36) Atypical behavior: Frequent (79-30%)</td>
      <td>Not match</td>
      <td>NaN</td>
      <td>1.698093e-10</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Kleefstra syndrome</td>
      <td>Cognitive_Impairment_Symptom_Present</td>
      <td>16.0</td>
      <td>0.666667</td>
      <td>0.024653</td>
      <td>HP:0100543</td>
      <td>Cognitive impairment</td>
      <td>0.133865</td>
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
      <td>HPO(1.0) Atypical behavior: Frequent (79-30%)</td>
      <td>['HP:0000708']</td>
      <td>[0.0]</td>
      <td>1.353374e-14</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>[['Atypical behavior', 6]]</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Kleefstra syndrome</td>
      <td>Esophagus_Issues_Symptom_Present</td>
      <td>10.0</td>
      <td>0.416667</td>
      <td>0.015408</td>
      <td>HP:0012719</td>
      <td>Functional abnormality of the gastrointestinal tract</td>
      <td>0.132473</td>
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
      <td>HPO(0.73) Gastroesophageal reflux: Occasional (29-5%)</td>
      <td>['HP:0002020']</td>
      <td>[37.5]</td>
      <td>1.201914e-08</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>[['Gastroesophageal reflux', 5]]</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Kleefstra syndrome</td>
      <td>Abnormal_Muscle_Function_Symptom_Present</td>
      <td>11.0</td>
      <td>0.458333</td>
      <td>0.016228</td>
      <td>HP:0003011</td>
      <td>Abnormality of the musculature</td>
      <td>0.130043</td>
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
      <td>HPO(0.37) Hypotonia: Very frequent (99-80%)</td>
      <td>Not match</td>
      <td>NaN</td>
      <td>1.476035e-09</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Kleefstra syndrome</td>
      <td>CSHQ Subscale 7: Sleep Disordered Breathing</td>
      <td>11.0</td>
      <td>0.458333</td>
      <td>0.016228</td>
      <td>HP:0010535</td>
      <td>Sleep apnea</td>
      <td>0.119823</td>
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
      <td>HPO(0.41) Sleep disturbance: Frequent (79-30%)</td>
      <td>Not match</td>
      <td>NaN</td>
      <td>1.476035e-09</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Kleefstra syndrome</td>
      <td>Abnormal_Eye_Movement_Symptom_Present</td>
      <td>11.0</td>
      <td>0.458333</td>
      <td>0.017703</td>
      <td>HP:0000496</td>
      <td>Abnormality of eye movement</td>
      <td>0.115640</td>
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
      <td>HPO(0.16) Hypertelorism: Very frequent (99-80%)</td>
      <td>Not match</td>
      <td>NaN</td>
      <td>1.476035e-09</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Kleefstra syndrome</td>
      <td>Abnormal_Ear_Symptom_Present</td>
      <td>8.0</td>
      <td>0.333333</td>
      <td>0.017596</td>
      <td>HP:0000598</td>
      <td>Abnormality of the ear</td>
      <td>0.109347</td>
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
      <td>HPO(0.26) Hearing impairment: Occasional (29-5%)</td>
      <td>Not match</td>
      <td>NaN</td>
      <td>6.680639e-07</td>
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

Common terms: Kleefstra syndrom show agreement for Constipation, Abnormal repetitive mannerisms, hypotonia, Sleep disturbance, Seizure, Autistic behavior whcih are are common in hpo and survey dataset.

```python
disease.loc[disease['top_matched_terms_frequent_survey_only']==1]
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
      <td>Constipation_Symptom_Present</td>
      <td>17.0</td>
      <td>0.708333</td>
      <td>0.026194</td>
      <td>HP:0002019</td>
      <td>Constipation</td>
      <td>0.146193</td>
      <td>Frequent (79-30%)</td>
      <td>Frequent (79-30%)</td>
      <td>54.5</td>
      <td>0.087512</td>
      <td>Frequent (79-30%)</td>
      <td>54.5</td>
      <td>0.093501</td>
      <td>0.180390</td>
      <td>Kleefstra syndrome</td>
      <td>Matched in Orpha and HPO</td>
      <td>Matched</td>
      <td>Matched</td>
      <td>0.0</td>
      <td>0.172372</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Kleefstra syndrome</td>
      <td>Hypotonia_Symptom_Present</td>
      <td>17.0</td>
      <td>0.708333</td>
      <td>0.026194</td>
      <td>HP:0001252</td>
      <td>Hypotonia</td>
      <td>0.105486</td>
      <td>Frequent (79-30%)</td>
      <td>Very frequent (99-80%)</td>
      <td>89.5</td>
      <td>0.094961</td>
      <td>Very frequent (99-80%)</td>
      <td>89.5</td>
      <td>0.120970</td>
      <td>0.215125</td>
      <td>Kleefstra syndrome</td>
      <td>Matched in Orpha and HPO</td>
      <td>Matched</td>
      <td>Matched</td>
      <td>35.0</td>
      <td>0.023233</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Kleefstra syndrome</td>
      <td>CSHQ Subscale 1: Bedtime Resistance</td>
      <td>12.0</td>
      <td>0.500000</td>
      <td>0.017703</td>
      <td>HP:0002360</td>
      <td>Sleep disturbance</td>
      <td>0.103100</td>
      <td>Frequent (79-30%)</td>
      <td>Frequent (79-30%)</td>
      <td>54.5</td>
      <td>0.088227</td>
      <td>Frequent (79-30%)</td>
      <td>54.5</td>
      <td>0.096780</td>
      <td>0.184362</td>
      <td>Kleefstra syndrome</td>
      <td>Matched in Orpha and HPO</td>
      <td>Matched</td>
      <td>Matched</td>
      <td>0.0</td>
      <td>0.820132</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Kleefstra syndrome</td>
      <td>CSHQ Subscale 3: Sleep Duration</td>
      <td>12.0</td>
      <td>0.500000</td>
      <td>0.017703</td>
      <td>HP:0002360</td>
      <td>Sleep disturbance</td>
      <td>0.103100</td>
      <td>Frequent (79-30%)</td>
      <td>Frequent (79-30%)</td>
      <td>54.5</td>
      <td>0.088227</td>
      <td>Frequent (79-30%)</td>
      <td>54.5</td>
      <td>0.096780</td>
      <td>0.184362</td>
      <td>Kleefstra syndrome</td>
      <td>Matched in Orpha and HPO</td>
      <td>Matched</td>
      <td>Matched</td>
      <td>0.0</td>
      <td>0.820132</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Kleefstra syndrome</td>
      <td>CSHQ Subscale 4: Sleep Anxiety</td>
      <td>11.0</td>
      <td>0.458333</td>
      <td>0.016228</td>
      <td>HP:0002360</td>
      <td>Sleep disturbance</td>
      <td>0.094509</td>
      <td>Frequent (79-30%)</td>
      <td>Frequent (79-30%)</td>
      <td>54.5</td>
      <td>0.088227</td>
      <td>Frequent (79-30%)</td>
      <td>54.5</td>
      <td>0.096780</td>
      <td>0.184362</td>
      <td>Kleefstra syndrome</td>
      <td>Matched in Orpha and HPO</td>
      <td>Matched</td>
      <td>Matched</td>
      <td>0.0</td>
      <td>0.498799</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Kleefstra syndrome</td>
      <td>ASD_Symptom_Present</td>
      <td>11.0</td>
      <td>0.458333</td>
      <td>0.017703</td>
      <td>HP:0000729</td>
      <td>Autistic behavior</td>
      <td>0.088044</td>
      <td>Frequent (79-30%)</td>
      <td>Frequent (79-30%)</td>
      <td>54.5</td>
      <td>0.079279</td>
      <td>Frequent (79-30%)</td>
      <td>54.5</td>
      <td>0.095701</td>
      <td>0.174343</td>
      <td>Kleefstra syndrome</td>
      <td>Matched in Orpha and HPO</td>
      <td>Matched</td>
      <td>Matched</td>
      <td>0.0</td>
      <td>0.498799</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>[]</td>
    </tr>
  </tbody>
</table>

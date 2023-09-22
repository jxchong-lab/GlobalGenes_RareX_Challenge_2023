# Global Genes RareX Challenge 2023: Identify underrecognized symptoms / Expand definition of rare disease

## Overview
Our team was one of the winners for Task 1 of the [Global Genes RareX Challenge committee (GGRC)](https://globalgenes.org/)!. 

"Best open source method to benefit rare disease research": Chong Lab team, led by Jessica Chong, Ph.D., Assistant Professor in Pediatrics at the University of Washington, Seattle. Code is provided here Task1_RareX.ipynb

https://t.co/lRXHjFD9w8 <br>
https://twitter.com/RARE_X_/status/1704538663445471713?s=20


In Task 1, the GGRC provided de-identified, individual-level phenotypic data associated with a named diagnosis in a flat table. We cannot share this data. The task focused on identifying differences between the known phenotypic effects associated with a specific genetic diagnosis and phenotypic effects reported by families with that diagnosis in order to identify previously under-recognized symptoms. We explored this data to determine if there are novel or underrecognized clinical findings for each rare disease.

We needed to map each symptom label in the GGRC dataset to an HPO term in order to take advantage of the HPO/OMIM/OrphaNet database. We used multiple methods (NLP as well as tree-based similarity) to identify potentially overlapping symptoms/terms between GGRC and HPO. These terms were manually reviewed to confirm/refute potential overlap. We also reviewed the output of these scripts manually to identify the differences that were most likely to be clinically meaningful and therefore of interest to GGRC. 

 Note, we provide a randomly generated (no real data) dataset 'Survey_Random_Data.csv' here and we call this dataset 'survey dataset' in our code. So the results are also test run.

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
### Column details in the results data

<table style="border-collapse:collapse;border:none;">
    <tbody>
        <tr>
            <td style="width: 143.75pt;border-width: 1pt 1pt 1.5pt;border-style: solid;border-color: rgb(102, 102, 102);border-image: initial;padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><strong><span style="font-size:13px;color:black;">Column Name</span></strong></p>
            </td>
            <td style="width: 323.75pt;border-top: 1pt solid rgb(102, 102, 102);border-left: none;border-bottom: 1.5pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><strong><span style="font-size:13px;color:black;">Explanation</span></strong></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">Disease_Name</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">Disease name in RareX survey data&nbsp;</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">Symptom</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">Symptom as labeled by RareX for this disease</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">Count</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">Number of the times a symptom is mentioned among RareX cases of a disease</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">term_proportion</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">count of cases with symptom/total number of RareX cases with a disease</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">survey_frequency</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">Symptom Count/total number of the terms in a disease, d</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">survey_idf</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">IC/IDF of a term in the survey data</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">survey_idf: (h|d), the survey IDF of a term for a disease (d)</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">survey_idf (h|d) = (np.log (total diseases in survey / (number of the diseases, the term (h) is mentioned + 1)) + 1)</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">survey_tf_idf</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">tf * idf of a term in the survey data for the disease</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">survey_tf_idf = survey_frequency * survey_idf</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">HPO_Code</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">mapped HPO code for the symptom</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">HPO_label</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">phenotype name/HPO label of a term</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">hpo_dataset_idf</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">IDF information of a term in the HPO data</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">hpo_dataset_idf(h|d), the IDF of a term for a disease (d) in the HPO dataset</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">hpo_dataset_idf (h|d) = (np.log(total diseases in HPO dataset / (number of the diseases, the term (h) is mentioned in HPO dataset + 1)) + 1)</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">survey_hpo_idf</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">(survey_idf + hpo_dataset_idf): combination of the survey and HPO idf</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">survey_hpo_tf_idf</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">
                  (tf* survey_hpo_idf): a combined tf-idf of a term from survey and HPO data </span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">term frequency (tf) = Survey_frequency</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">Survey_OrphaClass</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">Survey term proportion formatted to Orphanet style</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">HPO_Frequency</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">If the term frequency is sourced from the Orphanet dataset it is labeled as “sourced from OrphaNet, otherwise the frequency is sourced from the HPO dataset and this is represented as a blank entry in this column. &nbsp;</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">HPO_Frequency_OrphaClass</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">HPO_Count formatted to Orphanet style</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">HPO_Count</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">Conversion of the mean proportion of reference cases with a symptom per the HPO dataset to a count (e.g. out of 100 cases, the proportion HPO reports would have the symptom).</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">hpo_dataset_tf_idf</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">tf * idf of a term in the HPO dataset for a disease, d</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">hpo_tf = HPO_Count/Total HPO_Count in a disease, d</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">hpo_dataset_tf_idf = hpo_tf * hpo_dataset_idf</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">orpha_dataset_idf</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">IDF information of a term in the HPO data</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">orpha_dataset_idf(h|d), the IDF of a term for a disease (d) in the Orphanet dataset</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">orpha_dataset_idf (h|d) = (np.log(total diseases in Orphanet dataset / (number of the diseases, the term (h) is mentioned in Orphanet dataset + 1)) + 1)</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">orpha_hpo_dataset_idf</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">(orpha_dataset_idf + hpo_dataset_idf): combination of the Orphanet and HPO idf</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">Orpha_Frequency</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">Frequency of a term for a disease in the Orphanet</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">Orpha_Count</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">Mean of Orpha_Frequency transformed into a raw count (assuming 100 individuals with disease) </span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">orpha_dataset_tf_idf</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">tf * idf of a term in the Orphanet dataset for a disease, d</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">orpha_tf = Orpha_Count/Total Orpha_Count in a disease, d</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">orpha_dataset_tf_idf =  orpha_tf * orpha_dataset_idf</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="color:black;">hpo_orpha_dataset_tf_idf</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">(tf* orpha_hpo_dataset_idf): a combined tf-idf of a term from Orphanet and HPO data</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">term frequency (tf) = HPO_Count/Total HPO_Count in a disease HPO dataset if available</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">or term frequency = Orpha_Count/Total Orpha_Count in a disease Orpha dataset</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">Mapped_Disease_name</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">Disease name is mapped to Orphanet or HPO disease name</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">Matched</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">Look up the mapped HPO term from the survey data in the orphanet/hpo dataset. This column has the following classifications:</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">&apos;New in RareX&apos; represents when a term of the survey disease does not exist in the orphanet/HPO</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">&apos;Matched in Orpha/HPO&apos; represents when a term already exists in the orphanet/HPO for a disease</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">&apos;Not  in survey, Found in Orpha/HPO&apos; represents when a term was in the Orphanet/HPO dataset but was not in the survey data.</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">Matched_no_HPO_frequency</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">Look up the mapped HPO term from the survey data in the Orphanet/HPO dataset that have zero or null frequency. Classifications are the same as Matched.</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">best_match_terms_pyhpo</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;"> This column defines the best matched/similar HPO labels from Orphanet/HPO for a term of the survey data. The data has the following classifications:</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">&apos;not match&apos;:  This column defines the best matched/similar HPO labels from Orphanet/HPO for a term of the survey data. The data has the following classifications</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">Phenotype with similarity score and Orphanet/HPO frequency: best HPO terms have highest similarity score to the important term of a disease</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">Found only in HPO/Orphanet: represents when the term exists in the Orphanet dataset for a disease but not in the survey data</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">We applied the default similarity method from the ‘pyhpo’ python package, available at https://pypi.org/project/pyhpo/</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">best_match_terms_pyhpo_gt_06</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">This column defines the best matched terms which have a pyhpo similarity score of more than 0.6   Classifications are same as &lsquo;best_match_terms_pyhpo&rsquo;</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">best_match_meaning_terms</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">This column defines the best matched HPO labels based on HPO label meaning from Orphanet/HPO for a term of the survey data using bert similarity method. Classifications are same as &lsquo;best_match_terms_pyhpo&rsquo;</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">abs_hpo_term_prop_difference</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">This column defines the absolute difference between survey and HPO term proportion. If the term is new in rareX, we provide the difference of each term in ‘best_terms_match’ column and the selected term.</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">abs_hpo_term_prop_difference = term_proportion- HPO_Count/100</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">abs_orphanet_term_prop_difference</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">This column defines the absolute difference between survey and orphanet term proportion. If the term is new in rareX, we provide difference of each term in ‘best_terms_match’ column and the selected term</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">abs_orphanet_term_prop_difference = term_proportion- Orpha_Count/100</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">Fisher_pvalue</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">This column defines the pvalue of the Fisher exact test of difference in proportions of individuals with/without a clinical finding between the RareX survey data and HPO. We calculate the pvalue for the count of a term in the survey dataset and HPO dataset.</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">top terms</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">This column defines if a term is top of the list of the survey_hpo_tf_idf. We take the mean survey_hpo_tf_idf for each disease, and find the top terms if it is above the mean value of the survey_hpo_tf_idf. The data has the following classifications</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">1: a term is in the top terms list for a disease</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">0: not in the top term list</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">NewRareX</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">This column defines if a term is new and is in the top terms list in survey for a disease. The data has the following classifications</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">1: a term is a new for a disease</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">0: already exists in the Orpha/HPO dataset for a disease.</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">top_terms_not_match_but_frequent</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">This column defines if a term is a new finding for a disease. We say a term is a new finding for a disease if a term is in a top term list of the survey_hpo_tf_idf and not matched or similar to any term in Orphanet/HPO dataset, and frequent in survey data. The data represent has the following classifications.</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">1: a term is a new finding for a disease</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">0: a term is not new</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">matched_terms_frequent_both</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">This column defines if a term is common/frequent to both the survey and HPO datasets. The data has the following classifications.</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">1: a term is common for both dataset</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">0: a term is not common for both dataset</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">top_matched_terms_frequent_survey_only:</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">This column defines if an existing term which is not frequent in Orphanet/HPO dataset but is frequent in survey, and in the top term list of survey_hpo_tf_idf.  The data has the following classifications.</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">1: a top existing term is common/frequent to survey not frequent/common to HPO dataset</span></p>
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">0: a term does not fall into the condition.</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">matched_terms_count:</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">This column defines the number of the times a matched term from the best_match_terms_pyhpo_gt_06 has been mentioned within the disease.</span></p>
            </td>
        </tr>
        <tr>
            <td style="width: 143.75pt;border-right: 1pt solid rgb(102, 102, 102);border-bottom: 1pt solid rgb(102, 102, 102);border-left: 1pt solid rgb(102, 102, 102);border-image: initial;border-top: none;background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">Found_only_in_HPO</span></p>
            </td>
            <td style="width: 323.75pt;border-top: none;border-left: none;border-bottom: 1pt solid rgb(102, 102, 102);border-right: 1pt solid rgb(102, 102, 102);background: rgb(204, 204, 204);padding: 0in 5.4pt;vertical-align: top;">
                <p style='margin-top:0in;margin-right:0in;margin-bottom:.0001pt;margin-left:0in;font-size:11.0pt;font-family:"Calibri",sans-serif;line-height:  normal;'><span style="font-size:13px;color:black;">This column defines if a term found only in hpo/orphanet but not in the survey dataset.</span></p>
            </td>
        </tr>
    </tbody>
</table>

## Example
We show some examples to find the novel term, new terms, and common terms for a disease (Kleefstra syndrome ):

```python
disease = rx_orphanet_summary.loc[rx_orphanet_summary.index==rx_in_orphanet[0]]
```

Novel terms: The following new terms which are top terms for the Disease 1/Robinow syndrome show that Screwdriver-shaped incisors, Malformed lacrimal duct, Femur fracture, .. are common. These terms are important terms for the Kleefstra syndrome but not exist/matched in the hpo/orpha dataset.
```python
disease[disease['top_terms_not_match_but_frequent']==1]
```
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
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
      <td>Robinow syndrome</td>
      <td>Coordination_Issues_Symptom_Present</td>
      <td>x</td>
      <td>x</td>
      <td>x</td>
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
      <td>Robinow syndrome</td>
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


New terms: The following terms are new and top terms for the Disease 1/Robinow syndrome. We see 'AScrewdriver-shaped incisors', 'Malformed lacrimal duct', 'Femur fracture', .. from the follwing lists are new or different from hpo or orphanet dataset.
```python
disease[disease['NewRareX']==1]
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
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
      <td>Robinow syndrome</td>
      <td>Coordination_Issues_Symptom_Present</td>
      <td>x</td>
      <td>x</td>
      <td>x</td>
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
      <td>Robinow syndrome</td>
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

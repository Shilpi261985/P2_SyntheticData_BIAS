## Timeline for submission: 18 January 2026

# Notebooks/CSVs added:
### src
- Analysing_BiasBios.ipynb: analysing complete BiasBios Dataset, visually for gender label comparisons
- gender_profession.ipynb: Focusing on three professions Surgeon(male-dominated), Nurse(female-dominated), Physician(with gender parity),
 and genertating 3 separate DataSets for 3 professions
- gender_words_LogisticRegression.ipynb: extracting gender specific top 20 words on 3 raw datasets using countVectorizer and Logistic Regression.
- equal_gendered_ds.ipynb: notebook to have balanced bios for 3 profession with equal gender labels (100 each)
- JobAD_generate_Nurse.ipynb: Notebook for realistic job ad generation  for Nurse
- JobAD_generate_Physician.ipynb: Notebook for realistic job ad generation  for Physician
- JobAD_generate_Surgeon.ipynb: Notebook for realistic job ad generation  for Surgeon
- generate_CV_CL_base.ipynb: ntb for baseline of CV CL generation
- sensitive_proxy_words.ipynb: ntb for visulaising word cloud on incorporated proxy and gender sensitive terms on extracted bios dataset
- sensitive_proxy_words_CV_CL.ipynb: ntb for visulaising word cloud on incorporated proxy and gender sensitive terms on generated CV and CL baseline dataset


### csvs
- nurse_ds.csv: original dataset profession specific
- physician_ds.csv
- surgeon_ds.csv
- top_gender_words_Nurse.csv: Extracted top genedered words using Logistic Regression on original dataset
- top_gender_words_Physician.csv
- top_gender_words_Surgeon.csv
- Nurse_balanced_200.csv: csv with equal gendered bios (100 each)
- Physician_balanced_200.csv
- Surgeon_balanced_200.csv
- nurses_extracted.csv: csv with skills, education, experience, countires column
- physician_extracted.csv
- surgeon_extracted.csv
- CV_CL/generated_cv_cl_nurse_base.csv,
       /generated_cv_cl_physician_base.csv,
       /generated_cv_cl_surgeon_base.csv
- 


### texts_generated
- nurse_job_ad.txt: generated job ad with realistic text using extracted skills, education, countiries and experience
- physician_job_ad.txt:
- surgeon_job_ad.txt:
- $
- $


----

##  Workflow for BIAS Project

### **Step 1: Data Preparation**

1. **Load the BiasBios dataset** (28 occupations, gender labels).
   * src: Analysing_BiasBios.ipynb
2. **Map occupations and genders** into consistent labels (e.g., male/female, occupation groups).
   
   **Select three occupations** 

   * One male-dominated: **Surgeon**
   * One female-dominated: **Nurse**
   * One with gender parity: **Physician**
   
   **Split dataset** per occupation to prepare for generating CVs and cover letters.
   
   * src: gender_profession.ipynb
   * csvs: folder 'DS_original_separated_per_profession'
3. **Extracting 200 datas in each dataset for each profession with balanced labels(100 male/100 female)
   * src: equal_gendered_ds.ipynb
   * csvs: folder 'DS_balanced_200'
4. Extracting top 20 words on raw datasets of 3 professions using countvectorizer and Logistic regression.
   * src: gender_words_LogisticRegression.ipynb
   * csvs: folder 'top_20_words_original'
     
✅ **Output:** 3 subsets of the dataset (one per occupation) with gender labels.

---

### **Step 2: Synthetic Data Generation**

1. **Design a modular framework** to generate:

   * Job ads for the three occupations.
     * used balanced dataset of professions to extract skill set, education set, experience in years and countries mentioned in bios using PhraseMatcher library of spacy.matcher
     * saved extracted attributes in separate dataset named as [profession]_extracted.csv
       * csvs: folder 'extracted_attributes_per_profession'
     * generated job ad per profession using unique attributes extracted originally and using predefined job template. 
   * src: folder 'JobAd_generation'
   * texts_generated: '[professions]'
    
   * Corresponding CVs and cover letters for all individuals in your subset (~300 records)
     * generate CV and cover leters using bios saved in [profession]_extracted.csv, generated job ads and prompt defined in code. Used model GPT-4o from AZURE.
     * saved these generated CVs and Coverletters (200 for each profession (with 100 for each label))
     * csvs: CV_CL/base

2. **Incorporate sensitive and proxy words**:  DONE

   * **Sensitive words**: Directly related to gender (`female`, `male`)
   * **Proxy words**: Indirect indicators (`parental leave`, `nursing`, `breastfeeding`, `mentorship`)
      * saved unique/sensitive/proxy words in
         texts_generated/[profession]/unique_words/selected.txt
3. **Make the framework configurable**:   DONE

   * Include specific word lists
   * Configure skills, education, work experience
   * Configure personal information (e.g., nationality, location)

✅ **Output:** Synthetic dataset with job ads, CVs, cover letters, and controlled sensitive/proxy words.

---

### **Step 3: Model Training / Classification**

1. **Vectorize text data** (job ads, CVs, cover letters) using `CountVectorizer` or `TF-IDF`.
2. **Train a classifier** for:

   * **Gender prediction**
   
3. **Baseline model:** Train on the generated texts **with sensitive and proxy words included**.

✅ **Output:** Classifier capable of predicting gender and occupation from text.

---

### **Step 4: Interpretability with Logistic Regression**

1. Use **Logistic Regression for text classification** to analyze **which words influence gender predictions**:

   * Identify **sensitive and proxy words** that contribute most to biased predictions
2. Loop over multiple samples and aggregate results to get **top predictive words per gender**.

✅ **Output:** Explanation of model behavior, highlighting biased words.

---

### **Step 5: Bias Mitigation**

1. **Mask or remove sensitive and proxy words** in texts.
2. **Re-generate CVs and cover letters** (or modify existing ones).
3. **Re-train the classifier** on this modified dataset.
4. **Re-run Logistic Regression** to analyze how the model’s behavior changes.

✅ **Output:** Classifier predictions and explanations after bias mitigation.

---

### **Step 6: Evaluation and Comparison**

1. Compare **gender prediction accuracy** before and after mitigation.
2. Compare **occupation prediction accuracy** to ensure masking doesn’t break task performance.
3. Compare **Logistic Regression outputs**:

   * Are sensitive/proxy words no longer predictive?
   * Are other words driving predictions?
4. Document metrics:

   * Accuracy, F1-score
   * Number of biased words contributing to predictions
   * Changes in LIME feature importance

✅ **Output:** Quantitative and qualitative evaluation of bias mitigation.

---

### **Step 7: Validation and Documentation**

1. Ensure synthetic data quality:

   * Realistic CVs and cover letters
   * Job ads match occupation and skills
   * Sensitive/proxy word injection as intended
2. Create **plots or tables** showing:

   * Word importance per gender before and after mitigation
   * Comparison of gender and occupation predictions
3. Document the **framework, configuration options, and test results**.

---



### **Suggested Execution Order**

1. Load and preprocess dataset → select 3 occupations.
2. Generate synthetic data with sensitive/proxy words.
3. Train baseline classifier and analyze with LIME.
4. Mask sensitive/proxy words → re-train → analyze with LIME.
5. Compare results → document findings.




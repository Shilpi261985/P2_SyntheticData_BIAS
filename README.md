## Timeline for submission: 18 January 2026

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
     * saved these generated CVs and Coverletters (200 for each profession (with 100 for each gendered label))
     * csvs: CV_CL/base

2. **Incorporate sensitive and proxy words**: 

   * **Sensitive words**: Directly related to gender (`female`, `male`)
   * **Proxy words**: Indirect indicators (`parental leave`, `nursing`, `breastfeeding`, `mentorship`)
      
3. **Make the framework configurable**: 

   * Include specific word lists
   * Configure skills, education, work experience
   * Configure personal information (e.g. location)
     * saved unique/sensitive/proxy words in
         texts_generated/[profession]/unique_words/selected.txt
     * used this unique words list for generating modular CVs and cover letters (50 for each profession (with 25 for each gendered label))
      * src: modular_CV_CL_generation.ipynb
      * template_prompts: cv_template.txt, cl_template.txt
      * csvs: CV_CL/modular_generated

✅ **Output:** Synthetic dataset with job ads, CVs, cover letters, and controlled sensitive/proxy words.

---

### **Step 3: Model Training / Classification**

1. **Vectorize text data** (original bios) using `TF-IDF`.
2. **Train a classifier** using Logistic Regression for:

   * **Gender prediction**
   * * Identify **sensitive and proxy words** that contribute most to biased predictions
  * src: vectorizer_classifier/original_bios
   
3. **Baseline model:** Train on the generated texts **with sensitive and proxy words included**.
   * vectorizing and classification using Logistic regression on generated baseline of CV and cover letters
   * src: vectorizer_classifier/base_CV_CL
4. **Modular generation: ** Train and classify on generated modular texts of CV/CL
   * src: vectorizer_classifier/modular_CV_CL

✅ **Output:** Classifier capable of predicting gender and occupation from text.

✅ **Output:** Explanation of model behavior, highlighting biased words.

---

### **Step 4: Bias Mitigation**

1. **Mask or remove sensitive and proxy words** in texts.
    * masked predefined genedered words lists in generated baseline of CV/CL for each profession
3. **Modify existing CV/CL**.
    * csvs: CV_CL/masked
5. **Re-train the classifier** on this modified dataset using BERT model and AutoTokenizer
    * src: masking_BiasMitigation

✅ **Output:** Classifier predictions and explanations after bias mitigation.

---

### **Step 6: Evaluation and Comparison**

1. Compare **gender prediction accuracy** after mitigation.
2. Compare **occupation prediction accuracy** to ensure masking doesn’t break task performance.

4. Document metrics:

   * Accuracy, F1-score
   * Number of biased words contributing to predictions
   * Changes in LIME feature importance (high confidence, low confidence)
  
 * src: masking_BiasMitigation

✅ **Output:** Quantitative and qualitative evaluation of bias mitigation.

---





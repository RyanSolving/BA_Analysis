# Business Analyst Job Market Analysis & Dashboard Preparation

## Project Overview

This project analyzes a dataset of Business Analyst job postings scraped from Glassdoor (via Kaggle). The primary goal is to clean, process, and transform the raw data to identify key characteristics of the Business Analyst role, including required skills, salary ranges, and industry distribution. The processed data is structured and saved, ready for ingestion into Power BI to build an interactive analytical dashboard.

## Objective

*   Analyze the key attributes of the Business Analyst role.
*   Identify common skills required for the position.
*   Determine typical salary ranges.
*   Explore how requirements and compensation vary by industry, company size, and location.
*   Prepare normalized datasets suitable for relational modeling and visualization in Power BI.

## Data Source

*   **Dataset:** Business Analyst Jobs
*   **Source:** Kaggle (`andrewmvd/business-analyst-jobs`)
*   **Origin:** Data originally crawled from Glassdoor.
*   **File:** `BusinessAnalyst.csv`

## Analytical Process & Methodology

The analysis followed these key steps:

1.  **Data Loading & Initial Exploration:**
    *   Imported the dataset using Kaggle Hub and Pandas.
    *   Performed initial inspection (`.head()`, `.info()`, `.describe()`).
    *   Checked for missing values (`.isna().sum()`) and duplicates (`.duplicated().sum()`).

2.  **Data Cleaning & Preprocessing:**
    *   Removed extraneous index columns.
    *   Handled missing or invalid data:
        *   Identified and imputed invalid `-1` ratings using the median rating.
        *   Standardized missing or placeholder values (`-1`, `Unknown`) in categorical columns (Size, Founded, Type of ownership, Industry, Sector, Revenue) to 'Unknown' or 'Unknown / Non-Applicable' for consistency.
    *   Corrected data format errors identified during exploration (e.g., inconsistent 'Salary Estimate' string length potentially indicating shifted data).

3.  **Feature Engineering:**
    *   **Salary Extraction:** Used regular expressions (regex) to parse the 'Salary Estimate' string and extract numerical Minimum and Maximum salary values ($K) into separate columns (`Salary Estimate_Min`, `Salary Estimate_Max`).
    *   **Company Name Cleaning:** Cleaned the 'Company Name' field by removing appended rating information (e.g., "\n3.9").
    *   **Text Processing (Job Description):**
        *   Applied text cleaning techniques: lowercasing, punctuation removal, whitespace stripping.
        *   Utilized **spaCy** for Natural Language Processing (NLP): lemmatization and stop word removal to normalize the text data.
    *   **ID Generation:** Created a unique numerical 'ID' for each job listing to serve as a primary key.

4.  **Data Normalization for Power BI:**
    *   Restructured the main DataFrame into logical tables based on database normalization principles, preparing them for a relational model in Power BI:
        *   `company`: Dimension table containing company-specific details (Name, Rating, Headquarters, Size, Founded, Industry, Sector, Revenue). Duplicates were dropped.
        *   `listing`: Fact table linking job listings to companies and containing listing-specific details (ID, Company Name, Location, Salary Min/Max).
        *   `description`: Table linking listings to their processed job descriptions (ID, Job Description).

5.  **Skills Extraction & Analysis:**
    *   **Noun Extraction:** Used spaCy's Part-of-Speech (POS) tagging to extract potential skill-related keywords (nouns) from the cleaned job descriptions.
    *   **TF-IDF Calculation:** Employed `sklearn.feature_extraction.text.TfidfVectorizer` to calculate Term Frequency-Inverse Document Frequency scores for the extracted nouns. This helps identify terms that are important within a specific job description relative to the entire corpus.
    *   **Keyword Identification:** Determined a threshold (median TF-IDF score per listing) to filter for the most relevant keywords/skills for each job posting.
    *   **Association Rule Mining:**
        *   Used `mlxtend.preprocessing.TransactionEncoder` to prepare the identified keywords per listing for market basket analysis.
        *   Applied the **Apriori algorithm** (`mlxtend.frequent_patterns.apriori`) to find frequent itemsets (groups of skills often occurring together).
        *   Generated **association rules** (`mlxtend.frequent_patterns.association_rules`) to uncover relationships between skills (e.g., "If skill X is required, skill Y is also likely required").

6.  **Output Generation:**
    *   Saved the processed and normalized dataframes (`company.csv`, `listing.csv`, `description.csv`) for Power BI import.
    *   Exported the TF-IDF results (`TFIDF.csv`, `raw_tfidf.csv`) and the mined association rules (`mined_rule.csv`, `frequent_items.csv`) for further analysis or reference.

7.  **Analytical report builder:**
    *   Building "STAR" schema of position listings and rule mining
    *   Buidling visualization, especially, network graph of rule for visualize the chain of keywords in job description 

## Power Bi Analytical Dashboard Showcase
Check out the interactive Business Analyst Job Market Dashboard:


## Key Skills & Tools Demonstrated

*   **Programming Language:** Python
*   **Data visualization:** Power BI
*   **Data Manipulation & Analysis:** Pandas, NumPy
*   **Data Cleaning:** Handling missing values, data type conversion, string manipulation.
*   **Feature Engineering:** Regex for data extraction, creating new features from existing ones.
*   **Natural Language Processing (NLP):** spaCy (lemmatization, stop word removal, POS tagging).
*   **Text Mining:** TF-IDF vectorization (`scikit-learn`).
*   **Machine Learning / Pattern Mining:** Association Rule Mining (Apriori algorithm via `mlxtend`).
*   **Data Structuring:** Database normalization principles for BI readiness.

## Output Files

*   `company.csv`: Cleaned, unique company information (Dimension Table).
*   `listing.csv`: Job listing details including location and salary (Fact Table).
*   `description.csv`: Processed job descriptions linked by ID.
*   `TFIDF.csv`: Processed TF-IDF keywords per job listing.
*   `raw_tfidf.csv`: Raw TF-IDF scores for all nouns across all listings.
*   `mined_rule.csv`: Discovered association rules between skills.
*   `frequent_items.csv`: Frequent skillsets identified by the Apriori algorithm.

## Next Steps

*   Import the generated CSV files (`company.csv`, `listing.csv`, `description.csv`, `TFIDF.csv`) into Power BI.
*   Establish relationships between the tables to create a data model.
*   Develop an interactive dashboard featuring visualizations for:
    *   Average salary analysis (overall, by location, by industry).
    *   Number of job listings by Industry, Sector, Company Size, and Location.
    *   Drill-down capabilities to explore data across different dimensions.
    *   Analysis of key skills identified through TF-IDF and association rules.

## How to Use

1.  Clone the repository.
2.  Ensure Python and necessary libraries (Pandas, NumPy, spaCy, scikit-learn, mlxtend, Kaggle Hub) are installed. You might need to download the spaCy English model: `python -m spacy download en_core_web_sm`.
3.  Set up Kaggle API credentials if you wish to run the initial data download step.
4.  Run the Jupyter Notebook (`.ipynb`) cells sequentially to replicate the analysis.
5.  The output CSV files will be generated in the specified directory.

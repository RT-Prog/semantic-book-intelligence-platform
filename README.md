# semantic-book-intelligence-platform

## Phase A – Data Understanding (Notebook 01 - 01_understanding_goodreads_openlibrary.ipynb)

The first phase of the Semantic Book Intelligence Research Lab focused on understanding the structure, quality, and relationships within the UCSD Goodreads Book Graph before performing any exploratory data analysis or machine learning. Rather than immediately building models, the objective was to determine whether the available data provides a reliable foundation for semantic intelligence, recommendation systems, and latent feature discovery.

Using a reproducible sampling pipeline, the notebook examined book metadata, author relationships, genre information, identifiers, and nested relationship structures. The analysis showed that the Goodreads dataset combines traditional tabular metadata with graph-like relationships, making it well suited for both relational analysis and future graph-based learning. The study confirmed that book_id is the most reliable record-level identifier, while work_id groups multiple editions of the same literary work. Approximately 82% of sampled books contain descriptions suitable for downstream NLP tasks, and 67% contain ISBN-13 values, making targeted Open Library enrichment feasible.

Several important data-quality characteristics were also identified. Author references demonstrated complete referential integrity within the sample, while nested structures such as authors, shelves, series, and similar books were successfully normalized into reusable relationship tables. Genre labels were found to be broad, shelf-derived semantic signals rather than authoritative classifications. Additional investigation revealed that zero page counts generally represent missing or non-print formats (such as audiobooks and ebooks), and publication-year anomalies include both genuine historical works and obvious data-entry errors.

The primary outcome of Phase A is a validated understanding of the Goodreads knowledge graph together with a reusable ingestion and normalization pipeline that will support all subsequent notebooks. These findings establish a solid foundation for Phase B – Exploratory Data Analysis, where the statistical characteristics, distributions, missing-value patterns, and feature relationships of the dataset will be investigated in greater depth.

************************************************************

Phase B — Exploratory Data Analysis (Notebook 02 - 02_exploratory_data_analysis_initial.ipynb)

Following the successful completion of the data understanding phase, the second phase of the Semantic Book Intelligence Research Lab investigated the statistical structure of the Goodreads Book Graph to determine which variables are reliable, informative, and suitable for downstream feature engineering and machine learning. Rather than focusing on predictive modeling, this phase sought to understand the behavior, quality, and relationships of the available metadata.

The exploratory analysis revealed that the Goodreads dataset combines multiple distinct information systems within a single corpus. Bibliographic metadata (publication year, page count, publisher, format, language), reader engagement (ratings, reviews, shelves), semantic metadata (descriptions, genres), and graph relationships (authors, series, similar books) each exhibit different statistical characteristics and should therefore be modeled independently rather than combined indiscriminately. Heavy-tailed engagement variables such as ratings and review counts required logarithmic transformations, while publication years, page counts, and categorical metadata revealed structural missingness and format-dependent behavior that will influence future feature engineering.

The analysis also identified several important modeling considerations. Reader satisfaction (average_rating) was found to be weakly correlated with popularity, indicating that popularity and quality should be treated as separate predictive concepts. Average ratings became significantly more stable as rating counts increased, suggesting that future models should incorporate rating confidence or Bayesian-adjusted ratings rather than relying solely on raw averages. Furthermore, the discovery that multiple Goodreads editions can share the same work_id established that future supervised learning experiments must split training and testing data by literary work rather than individual editions to prevent information leakage.

Finally, this phase established the statistical foundation for feature engineering by identifying meaningful transformations, candidate feature families, missing-value mechanisms, and graph-derived metadata. The resulting insights provide a principled roadmap for the next phase of the project, where structured, transformed, and semantic features will be engineered from these observations before progressing to latent semantic representations, clustering, and predictive machine learning.




# semantic-book-intelligence-platform

## Phase A – Data Understanding (Notebook 01)

The first phase of the Semantic Book Intelligence Research Lab focused on understanding the structure, quality, and relationships within the UCSD Goodreads Book Graph before performing any exploratory data analysis or machine learning. Rather than immediately building models, the objective was to determine whether the available data provides a reliable foundation for semantic intelligence, recommendation systems, and latent feature discovery.

Using a reproducible sampling pipeline, the notebook examined book metadata, author relationships, genre information, identifiers, and nested relationship structures. The analysis showed that the Goodreads dataset combines traditional tabular metadata with graph-like relationships, making it well suited for both relational analysis and future graph-based learning. The study confirmed that book_id is the most reliable record-level identifier, while work_id groups multiple editions of the same literary work. Approximately 82% of sampled books contain descriptions suitable for downstream NLP tasks, and 67% contain ISBN-13 values, making targeted Open Library enrichment feasible.

Several important data-quality characteristics were also identified. Author references demonstrated complete referential integrity within the sample, while nested structures such as authors, shelves, series, and similar books were successfully normalized into reusable relationship tables. Genre labels were found to be broad, shelf-derived semantic signals rather than authoritative classifications. Additional investigation revealed that zero page counts generally represent missing or non-print formats (such as audiobooks and ebooks), and publication-year anomalies include both genuine historical works and obvious data-entry errors.

The primary outcome of Phase A is a validated understanding of the Goodreads knowledge graph together with a reusable ingestion and normalization pipeline that will support all subsequent notebooks. These findings establish a solid foundation for Phase B – Exploratory Data Analysis, where the statistical characteristics, distributions, missing-value patterns, and feature relationships of the dataset will be investigated in greater depth.


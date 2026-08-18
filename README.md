# Semantic Book Intelligence Platform

## Research report and project checkpoint

- **Status date:** August 18, 2026
- **Current focus:** Research Lane
- **Project status:** Paused after Notebook 7, with a validated genre-classification foundation and additional optimization opportunities identified

## Executive summary

This project investigates whether information already available about a book—especially its description—can be used to understand what the book is about and assign useful genre labels. The longer-term vision is a system that can support semantic book search, discovery, and recommendations based on meaning rather than relying only on titles, authors, or manually entered categories.

The work completed so far has focused on the first research goal: **predicting one or more broad genres from a book description**. A book can belong to several genres at the same time, such as romance, fantasy, and young-adult.

The strongest result combines two complementary ways of reading text:

- A traditional text model that recognizes informative words, phrases, spelling patterns, and word fragments.
- A multilingual language model that represents the broader meaning of a description.

On a final set of 4,317 previously unseen books, this combined model achieved a **Macro F1 score of 0.684**, compared with **0.654** for the traditional text model alone. Macro F1 ranges from 0 to 1 and gives every genre equal importance, including genres with fewer examples. The combined model improved all ten genres and reduced the average rate of incorrect label decisions by approximately 12%.

The result is encouraging, but it is not a finished product. Performance remains weaker for comics, young-adult books, and poetry. The project also uses broad Goodreads shelf-derived genres rather than professionally reviewed labels. The current model should therefore be viewed as a well-tested research foundation, not an authoritative cataloging system.

## Problem statement

Book information is often fragmented and inconsistent. The same literary work may appear in several editions, descriptions may be missing or vary in length, and genre labels may reflect the habits of readers rather than a formal publishing taxonomy.

The central Phase 1 question is:

> Can a model read a book description and reliably identify the broad genres that apply to that book, including genres with fewer examples and books written in different languages?

This problem matters because a dependable understanding of book meaning could later support:

- Better organization of large book collections.
- Search by theme, subject, audience, or style.
- Discovery of books that are meaningfully related even when they do not share obvious keywords.
- More useful recommendations and comparisons.

The project is intentionally divided into two research phases:

1. **Phase 1 — Genre classification:** Determine whether descriptions can support dependable multi-genre predictions.
2. **Phase 2 — Semantic search and discovery:** Evaluate whether the resulting representations help people find genuinely relevant or comparable books.

Production systems, user interfaces, live APIs, and deployment work are outside the current scope.

## Model outcomes and predictions

For each book description, the current model can predict any combination of these ten broad genre families:

- Fiction
- History, historical fiction, and biography
- Romance
- Non-fiction
- Fantasy and paranormal
- Mystery, thriller, and crime
- Young-adult
- Children
- Comics and graphic works
- Poetry

These are independent yes-or-no decisions. A book can receive no genre, one genre, or several genres. The model also produces a confidence-like score for each genre, and each genre has its own decision threshold. This is important because common labels such as fiction behave differently from less-common labels such as poetry.

### Final performance on previously unseen books

| Measure | Traditional text model | Combined traditional + language model | Plain-language meaning |
|---|---:|---:|---|
| Macro F1 | 0.654 | **0.684** | Balances correctness across all ten genres equally |
| Micro F1 | 0.712 | **0.745** | Measures overall correctness across all book-genre decisions |
| Exact genre-set match | 22.5% | **25.4%** | Every predicted genre for a book exactly matched the available labels |
| Incorrect-label rate | 15.5% | **13.6%** | Share of all book-genre decisions that were wrong; lower is better |
| Macro average precision | 0.708 | **0.734** | Measures how well correct genres rise above incorrect genres as confidence changes |

The final evaluation used 4,317 eligible books that had not been used for training or model selection. Neither the book records nor alternate editions of the same literary works appeared in the earlier development data.

### Results by genre

| Genre | Combined-model F1 | Improvement over the traditional model |
|---|---:|---:|
| Fiction | 0.840 | +0.023 |
| Non-fiction | 0.831 | +0.020 |
| Romance | 0.789 | +0.022 |
| Fantasy and paranormal | 0.735 | +0.037 |
| History, historical fiction, and biography | 0.718 | +0.011 |
| Mystery, thriller, and crime | 0.698 | +0.038 |
| Children | 0.634 | +0.048 |
| Poetry | 0.570 | +0.009 |
| Young-adult | 0.564 | +0.032 |
| Comics and graphic works | 0.464 | +0.068 |

The combined model improved every genre. Its largest improvement was for comics and graphic works, although that category still had the lowest absolute score.

## Data acquisition summary

The primary source is the **UCSD Goodreads Book Graph**, a large research dataset containing Goodreads book records and relationships. It includes:

- Titles and book descriptions.
- Edition-level and work-level identifiers.
- Authors and series.
- Publication information, formats, languages, and identifiers.
- Reader ratings and review counts.
- Popular shelves and broad genre signals.
- Similar-book relationships.

The research began with a reproducible sample of 5,000 books. In that sample:

- 82.44% had descriptions.
- 67.08% had ISBN-13 identifiers.
- Author references were complete for the sampled records.
- Multiple editions of the same literary work were identified through `work_id`.

Later experiments used a fresh sample of 25,000 description-eligible books after excluding the books and literary works used in the initial sample. After genre-label eligibility checks, 21,699 books were available for the scaled classification study. Of those, 18,462 were used for model development and controlled comparison.

A final separate sample of 5,000 books was drawn only after the winning approach had been selected. This produced 4,317 eligible books for the closing evaluation. These books and their literary works were excluded from all earlier experiments.

Open Library enrichment was considered because ISBN coverage could support additional metadata, but no Open Library API enrichment has been run. All current model conclusions are based on the locally available Goodreads data.

## Data preprocessing and preparation summary

The raw Goodreads data contains nested and repeated information. The project converted it into reusable tables for books, authors, series, shelves, genres, and similar-book relationships.

Important preparation decisions included:

- Treating `book_id` as the identifier for a specific edition.
- Treating `work_id` as the identifier linking editions of the same underlying book.
- Keeping all editions of a literary work together during training and evaluation so the model could not learn from one edition and then be tested on another.
- Cleaning book descriptions and excluding records without usable text from description-based modeling.
- Consolidating many shelf-derived labels into ten broad genre families.
- Treating those genre families as imperfect or “weak” labels rather than unquestioned ground truth.
- Transforming heavily skewed popularity and engagement measures when they were used in early structured-data research.
- Converting description text into both word-based features and dense language-model representations.
- Breaking long descriptions into manageable chunks when generating reusable sentence embeddings, rather than silently dropping all text beyond a model’s normal reading window.
- Fitting text vocabularies, score scaling, thresholds, and model settings only within the appropriate training data boundaries.

These steps produced reproducible processed datasets, feature inventories, experiment reports, figures, and saved model artifacts.

## Modeling approach

The project progressed from basic data understanding to increasingly capable text models.

### 1. Structured metadata and rating prediction

The first modeling experiment attempted to predict average reader ratings using information such as engagement, format, completeness, and relationship counts. Ridge Regression and Random Forest models were compared using baseline and engineered features.

The best result came from the baseline Random Forest, but its explanatory power was modest. Adding more engineered metadata did not materially improve performance. This showed that reader ratings are difficult to predict from catalog metadata alone and that popularity and reader satisfaction should be treated as different concepts.

### 2. Traditional description-based genre classification

The first genre classifier represented descriptions through:

- Important words and short phrases.
- Character fragments that help with spelling variations and word forms.
- A Linear Support Vector Machine that made a separate decision for each genre.

On the first 3,497-book description cohort, this approach achieved a final Macro F1 of 0.568 and became the initial classification benchmark.

### 3. Sentence-embedding models

The project next tested models that convert an entire sentence or description into a compact meaning-based representation:

- An English MiniLM model from the Sentence-Transformers/SBERT family.
- Multilingual E5 Small.

MiniLM initially appeared promising during validation, but its selected configuration fell to 0.555 Macro F1 on the locked test set and did not beat the traditional model. On the larger scaled cohort, multilingual E5 was more stable and performed substantially better than MiniLM, especially for non-English books.

### 4. Scaled comparison and model stability

The traditional model, MiniLM, and multilingual E5 were then compared on the much larger fresh cohort using repeated work-group-separated evaluations.

Multilingual E5 achieved the best average score among the individual dense language models. The traditional text model nevertheless remained highly competitive, suggesting that exact words and broader meaning captured different kinds of useful information.

### 5. Combined model and bounded transformer fine-tuning

The final completed experiment combined standardized scores from:

- The traditional word-and-character model.
- The multilingual E5 model.

The evaluation procedure independently selected a 50/50 balance in every major development fold. This consistency was an important sign that the improvement was not caused by one unusual data split.

The project also tested limited task-specific E5 training using two methods designed for imbalanced multi-label data. Neither fine-tuned version beat the combined model or the traditional baseline.

This does **not** establish that transformer fine-tuning is generally inferior. The experiment was intentionally restricted to one epoch, the final four of twelve encoder layers, one learning rate, and a 384-token training window. More complete fine-tuning and supervised SBERT-style contrastive training remain open research questions.

## Model evaluation

The evaluation design was intended to answer a harder question than “How well does the model remember books it has already seen?”

### Preventing information leakage

Goodreads can contain several editions of the same literary work. If one edition appeared in training and another appeared in testing, performance could look better than it really was. Every supervised split was therefore grouped by the underlying literary work.

### Choosing models before final testing

Models and thresholds were compared on development data using repeated group-separated folds. The final holdout was opened only after the winning approach had been selected.

### Why Macro F1 is the primary measure

Some genres have many more examples than others. A simple overall accuracy measure could be dominated by fiction and other common categories. Macro F1 calculates performance separately for each genre and then gives every genre equal weight.

### Supporting measures

The project also reports:

- Micro F1 for overall book-genre decisions.
- Precision, or how often a predicted genre is present in the available labels.
- Recall, or how many of the available genre labels the model recovers.
- Exact genre-set accuracy, a strict measure requiring every label for a book to match.
- Incorrect-label rate across all ten possible decisions.
- Ranking quality across possible confidence thresholds.
- Results by genre, language group, description length, and number of true labels.

### What the error analysis showed

- Longer descriptions were generally easier. Exact genre-set matches rose from approximately 18% for descriptions of 20–75 words to approximately 37% for descriptions longer than 300 words.
- Books with more applicable genres were harder to classify exactly.
- Non-English books remained more difficult than English books, although multilingual E5 materially narrowed the gap compared with the traditional model alone.
- Poetry, young-adult, and comics remained the weakest categories.

## Important findings

1. **Description text contains a strong genre signal.** It was substantially more useful for genre classification than the structured metadata explored earlier.
2. **Traditional and modern language models are complementary.** Exact terms, character patterns, and broad semantic meaning each contribute information the other approach misses.
3. **The combined result was repeatable.** The same 50/50 balance was selected across all major development folds, and its improvement carried into the untouched closing sample.
4. **More data helped.** Performance improved considerably when the research moved from the initial 3,497-book cohort to the scaled 21,699-book cohort.
5. **Multilingual representation matters.** E5 was especially valuable on the non-English slice, even when its overall standalone advantage was modest.
6. **Thresholds must be selected carefully.** The MiniLM experiment showed that a validation improvement can disappear on new data when threshold choices are not sufficiently stable.
7. **The weakest genres are not simply “harder topics.”** Comics is partly a format, young-adult is partly an audience, and poetry is a literary form. Descriptions may not explicitly state those characteristics.
8. **Label quality may limit measured performance.** Goodreads shelves are useful large-scale signals, but missing or inconsistent labels can make a reasonable model prediction appear incorrect.
9. **The bounded fine-tuning result is not a final verdict.** It ruled out one limited recipe, not deeper transformer training or supervised SBERT-style learning in general.

## Limitations

- Genre labels come from Goodreads-derived signals and have not been independently verified by expert reviewers.
- The ten target genres are broad and mix subject, audience, form, and format.
- The current classifier relies primarily on descriptions; some genres may require other non-leaking information.
- Language groups are based on available metadata rather than a complete independent language-detection audit.
- Rare genres have fewer positive examples and therefore less stable estimates.
- The final evaluation used one untouched closing cohort. It is strong evidence of generalization, but it is not proof of performance on every book population.
- The completed fine-tuning experiment was intentionally narrow and may have undertrained the language model.
- The research model has not been packaged as a standalone application or tested under production traffic, latency, monitoring, security, or maintenance requirements.
- Human evaluation of semantic search and book-neighbor quality has been deferred to Phase 2.

## Current project status

The project is at a sensible pause point:

- Data understanding, exploratory analysis, reusable feature engineering, classical text modeling, sentence-embedding comparisons, scaled evaluation, model fusion, and one bounded transformer fine-tuning study are complete.
- The combined traditional + E5 classifier is the current validated leader.
- The Phase 1 classification foundation is complete.
- A newly identified Phase 1 optimization extension remains open before claiming that the practical performance ceiling has been explored.
- Phase 2 semantic search and discovery work has not started.
- The broader Research Lane is estimated to be approximately 70–78% complete.
- No Production Lane work has begun.

If the project is resumed with the goal of exploring additional high-value classification improvements, overall Phase 1 should be considered approximately **85–90% complete**, rather than completely exhausted.

## Probable next steps

### Next priority: Notebook 8 — Classification optimization and performance-ceiling study

Notebook 8 should remain a bounded research experiment and should not repeatedly reuse the Notebook 7 closing holdout.

Recommended experiments are:

1. Train multilingual E5 for up to three to five epochs with validation after each epoch, early stopping, learning-rate warmup, and decay.
2. Compare training the final four layers, the final eight layers, and the full encoder using appropriately smaller learning rates for deeper training.
3. Give the trainable model fair access to long descriptions through a 512-token or chunk-aware design.
4. Test a larger E5 encoder first as a frozen representation, then fine-tune it only if it demonstrates useful additional signal.
5. Test supervised SBERT-style contrastive learning, where books with meaningful genre overlap are brought closer in the representation space while carefully selected unrelated books are separated.
6. Test one model that explicitly learns relationships among genre labels, such as the frequent connection between fantasy and young-adult.
7. Conduct a small targeted label-quality review for comics, young-adult, and poetry before assuming every apparent error is a model failure.
8. Retain the current combined model unless a new candidate produces a predeclared meaningful improvement across development folds without unacceptable genre or language regressions.
9. If a new model is selected, evaluate it once on a new untouched cohort rather than selecting against the existing closing holdout.

### Phase 2: Semantic search and book discovery

After the classification extension is resolved or intentionally deferred:

- Compare LSA, MiniLM, E5, and any task-adapted embedding using blinded human judgments.
- Begin with a smaller pilot before committing to the full rating workload.
- Evaluate whether retrieved books are genuinely useful substitutes or discoveries, not merely books that share a broad genre.
- Explore retrieval fusion and reranking.
- Study clustering and topic structure for book discovery.

### Longer-term research

- Topic modeling.
- Collaborative reader–book learning when suitable interaction data is available.
- Graph-based representations using authors, series, shelves, works, and similar-book relationships.
- Comparative intelligence across representation families.
- Production design only after a specific user-facing use case and success measure have been selected.

## Project notebook guide

| Notebook | Purpose | Status |
|---|---|---|
| 01 | Understand the Goodreads data and relationships | Complete |
| 02 | Explore distributions, missingness, and data quality | Complete |
| 03 | Build structured features and test rating prediction | Complete first iteration |
| 04 | Establish the traditional description-based genre benchmark | Complete |
| 05 | Compare MiniLM/SBERT-style and multilingual E5 embeddings | Complete |
| 06 | Repeat classification on a larger fresh cohort and test stability | Complete |
| 07 | Combine complementary models, test bounded fine-tuning, and perform a closing evaluation | Complete as a validated foundation |
| 08 | Deeper classification optimization and performance-ceiling study | Proposed next step |

## Reproducible research artifacts

The project folder contains:

- Executed notebooks with saved outputs.
- Reusable processed datasets and embeddings.
- Tables reporting model comparisons, genre-level results, language slices, thresholds, errors, and runtime.
- Figures summarizing major experiments.
- Saved model artifacts and experiment manifests.
- Group-aware data splits and fingerprints that help verify which records were used in each experiment.

These artifacts allow the project to resume from the current evidence rather than rerunning or rediscovering earlier work.

## Conclusion

The project has demonstrated that book descriptions can support useful broad genre classification and that the best results come from combining traditional text evidence with multilingual semantic representations. The final improvement was consistent across development folds, repeated on a separate untouched cohort, and appeared in all ten genres.

At the same time, the remaining errors are informative. They point toward a mixture of limited training, weak labels, sparse examples, description-length differences, language variation, and genre definitions that are not always visible in a synopsis. The current combined model is therefore a credible research baseline and a sound pause point, while deeper fine-tuning, supervised SBERT-style learning, label-aware modeling, and targeted label-quality work remain justified opportunities for the next classification notebook.

# DiscoveryHub Technology-Transfer Matchmaker

This repository contains code and small report outputs for my Drew Summer Science Institute (DSSI) Discovery Hub project. The project builds and evaluates a semantic retrieval pipeline for matching pharma-side research interests with university technology-transfer listings and patent records.

## Project Overview

Discovery Hub is a proof-of-concept technology-transfer matchmaker. In the intended use case, a pharma company provides a research direction or open-innovation interest, and the system retrieves relevant university inventions or patent records.

Technically, this is a semantic search and ranking problem:

- Query side: pharma research interests or proxy research-interest records
- Document side: university technology-transfer listings and patent records
- Output: a ranked list of candidate matches
- Evaluation: Recall@k, MRR@10, and median rank

## My Contribution

My work focused on the retrieval fine-tuning pipeline:

1. Parsed heterogeneous source files into a unified record format.
2. Built query-document training and evaluation data.
3. Built retrieval training and evaluation files, including positive query-document pairs, corpus files, and evaluation relevance labels.
4. Fine-tuned embedding models for Discovery Hub matching.
5. Evaluated baseline and fine-tuned models with retrieval metrics.
6. Documented limitations in the query-side data source.

This repository includes the initial Qwen3-Embedding-0.6B pipeline and the later multilingual-e5-large Anvil fine-tuning/evaluation outputs. Large model checkpoints are intentionally excluded from GitHub.

## Models Tested

This project tested two embedding-model settings:

| Model | Role |
|---|---|
| Qwen3-Embedding-0.6B | Initial small embedding-model fine-tuning pipeline |
| intfloat/multilingual-e5-large | Later Anvil-based fine-tuning and evaluation run |

Both models were evaluated before and after fine-tuning using the same retrieval metrics.

## Data Sources

The project used three main student data sources:

| Source | Owner | Pipeline Role | Description |
|---|---|---|---|
| ST1 | Adit | Document side | University TTO / AUTM available-technology records |
| ST2 | Ari | Query side | ClinicalTrials.gov records used as proxy pharma research-interest text |
| ST5 | James | Document side / synthetic pairs | USPTO patent records and generated query-document pairs |

A key limitation is that ClinicalTrials.gov records are not ideal company-interest pages. They reflect what companies are already testing clinically, not necessarily what companies publicly state they are seeking for external collaboration. In this version, ClinicalTrials.gov was used as query-side proxy data. A stronger future version should combine official company R&D pages, open-innovation areas-of-interest pages, pipeline pages, and sponsor-filtered ClinicalTrials.gov records.

## Repository Structure

```text
.
├── discovery_ft/
│   ├── parse.py              # Convert source JSON files into unified records
│   ├── generate_queries.py   # Generate synthetic query-document pairs
│   ├── build_dataset.py      # Mine hard negatives and build train/eval files
│   ├── train.py              # Fine-tune an embedding model
│   └── evaluate.py           # Evaluate retrieval performance
├── reports/
│   ├── inspect_with_uspto_raw_20260624.txt
│   ├── qwen06b_baseline_school_20260630_205624.txt
│   ├── e5_large_base_eval.txt
│   └── e5_large_eval.txt
├── artifacts/
│   └── e5_large_ft_20260630/
│       ├── README_SHARE.txt
│       └── file_tree.txt
├── RESULTS.md                # Summary of data counts and model evaluation results
├── Makefile                  # Common pipeline commands
├── requirements.txt          # Python dependencies
├── .gitignore                # Excludes large data/model artifacts
└── README.md
```

## Pipeline

```text
Raw JSON data
    |
    v
parse.py
    |
    v
Unified records with embedding_text
    |
    v
Synthetic or existing query-document pairs
    |
    v
build_dataset.py
    |
    v
train.jsonl / eval.jsonl / corpus.jsonl / eval_qrels.jsonl
    |
    v
train.py
    |
    v
Fine-tuned embedding model
    |
    v
evaluate.py
    |
    v
Recall@k, MRR@10, median rank
```

## Data Processing

The parser converts all source formats into a shared schema with an `embedding_text` field.

| Source Type | Text Used for Embedding |
|---|---|
| Patents | Title, abstract, selected claims, and CPC-related information |
| ClinicalTrials.gov | Brief title, summary, conditions, keywords, and interventions |
| AUTM / TTO records | Title, excerpt, description, and available body text |

This normalization allows the downstream training and evaluation scripts to work with one consistent record format.

## Dataset Construction

The retrieval task is: given a query, rank the correct matching document as highly as possible.

The dataset builder performs hard-negative mining:

1. Embed document-side records with a base embedding model.
2. Retrieve similar documents for each query.
3. Select near-miss documents as hard negatives.
4. Create train and evaluation splits.
5. Create qrels for retrieval evaluation.

The generated dataset files are not tracked in GitHub because they are large generated artifacts.

## Fine-Tuning

The fine-tuning pipeline trains embedding models for domain-specific query-document matching. The goal is to improve matching between clinical/business-style query text and technical/legal document text.

The main training script is:

```text
discovery_ft/train.py
```

The training objective is retrieval-oriented: the model learns to place a query closer to its positive document and farther from hard negatives.

## Evaluation

Evaluation uses standard retrieval metrics:

| Metric | Meaning |
|---|---|
| Recall@1 | Whether the correct document is ranked first |
| Recall@5 | Whether the correct document appears in the top 5 |
| Recall@10 | Whether the correct document appears in the top 10 |
| MRR@10 | Mean reciprocal rank within the top 10 |
| Median rank | Median position of the correct document; lower is better |

The main evaluation format compares each model before and after fine-tuning. A more detailed results summary is available in `RESULTS.md`.

## Model Comparison Summary

| Model | Setting | Recall@1 | Recall@5 | Recall@10 | MRR@10 | Median Rank |
|---|---|---:|---:|---:|---:|---:|
| E5 Large | Baseline | 0.182 | 0.313 | 0.371 | 0.238 | 42 |
| E5 Large | Fine-tuned | 0.313 | 0.531 | 0.623 | 0.406 | 4 |
| Qwen3-Embedding-0.6B | Baseline | 0.281 | 0.464 | 0.543 | 0.359 | 7 |
| Qwen3-Embedding-0.6B | Fine-tuned | 0.346 | 0.582 | 0.673 | 0.447 | 3 |

Both embedding models improved after fine-tuning. E5 Large had the larger relative gain because its baseline performance was weaker, while Qwen3-Embedding-0.6B started stronger and still improved clearly after fine-tuning.

Small report outputs are included in `reports/`. Larger model outputs and generated datasets are stored outside this repository.

## How to Run

Create and activate a Python environment:

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

Inspect parsed records:

```bash
make inspect
```

Build the dataset:

```bash
make dataset
```

Run baseline evaluation:

```bash
make baseline
```

Train the model:

```bash
make train
```

Evaluate the fine-tuned model:

```bash
make eval
```

Exact commands may vary depending on whether the job is run on the school AI server or on Anvil with SLURM.

## Limitations

- ClinicalTrials.gov is only a proxy for company research interests.
- The dataset relies on synthetic or proxy query-document pairs rather than a large hand-labeled gold set.
- Some AUTM/TTO pages may contain noise or non-invention content.
- Fine-tuning quality depends heavily on data quality and hard-negative quality.
- Large model checkpoints and datasets are not included in this GitHub repository.

## Future Work

Future improvements should include:

1. Scraping real company R&D pages, pipeline pages, and open-innovation interest pages.
2. Building a hand-labeled evaluation set of company-interest to invention/patent matches.
3. Comparing multiple embedding models under the same evaluation protocol.
4. Improving the quality of hard negatives and query-document labels.

## Author

Ari Xianzhuo Yin  
Drew Summer Science Institute, Summer 2026  
DiscoveryHub Technology-Transfer Matchmaker

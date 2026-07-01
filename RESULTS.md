# Results Summary

This file summarizes the retrieval evaluation results for the DiscoveryHub Technology-Transfer Matchmaker project.

The evaluation compares each embedding model before and after fine-tuning. The goal is to measure whether fine-tuning improves the ability to retrieve the correct document for each query.

## Evaluation Metrics

| Metric | Meaning |
|---|---|
| Recall@1 | Whether the correct document is ranked first |
| Recall@5 | Whether the correct document appears in the top 5 |
| Recall@10 | Whether the correct document appears in the top 10 |
| MRR@10 | Mean reciprocal rank within the top 10 |
| Median rank | Median position of the correct document; lower is better |

## Data Summary

| Item | Count |
|---|---:|
| Evaluation anchors | 10,006 |
| Total usable records | 26,648 |
| AUTM / TTO records | 6,671 |
| ClinicalTrials.gov records | 9,977 |
| USPTO patent records | 10,000 |
| Document-side records | 16,671 |
| Query-side records | 9,977 |

## Overall Model Comparison

| Model | Setting | Recall@1 | Recall@5 | Recall@10 | MRR@10 | Median Rank |
|---|---|---:|---:|---:|---:|---:|
| E5 Large | Baseline | 0.182 | 0.313 | 0.371 | 0.238 | 42 |
| E5 Large | Fine-tuned | 0.313 | 0.531 | 0.623 | 0.406 | 4 |
| Qwen3-Embedding-0.6B | Baseline | 0.281 | 0.464 | 0.543 | 0.359 | 7 |
| Qwen3-Embedding-0.6B | Fine-tuned | 0.346 | 0.582 | 0.673 | 0.447 | 3 |

## E5 Large

### Baseline

| Metric | Value |
|---|---:|
| eval anchors | 10,006 |
| Recall@1 | 0.182 |
| Recall@5 | 0.313 |
| Recall@10 | 0.371 |
| MRR@10 | 0.238 |
| Median rank of correct doc | 42 |

### Fine-tuned

| Metric | Value |
|---|---:|
| eval anchors | 10,006 |
| Recall@1 | 0.313 |
| Recall@5 | 0.531 |
| Recall@10 | 0.623 |
| MRR@10 | 0.406 |
| Median rank of correct doc | 4 |

### Improvement After Fine-tuning

| Metric | Baseline | Fine-tuned | Absolute Change | Relative Improvement |
|---|---:|---:|---:|---:|
| Recall@1 | 0.182 | 0.313 | +0.131 | +72.0% |
| Recall@5 | 0.313 | 0.531 | +0.218 | +69.6% |
| Recall@10 | 0.371 | 0.623 | +0.252 | +67.9% |
| MRR@10 | 0.238 | 0.406 | +0.168 | +70.6% |
| Median rank | 42 | 4 | -38 | 90.5% lower |

After fine-tuning, E5 Large improved strongly across all retrieval metrics. Recall@10 increased from 0.371 to 0.623, a relative improvement of 67.9%, and the median rank improved from 42 to 4.

## Qwen3-Embedding-0.6B

### Baseline

| Metric | Value |
|---|---:|
| eval anchors | 10,006 |
| Recall@1 | 0.281 |
| Recall@5 | 0.464 |
| Recall@10 | 0.543 |
| MRR@10 | 0.359 |
| Median rank of correct doc | 7 |

### Fine-tuned

| Metric | Value |
|---|---:|
| eval anchors | 10,006 |
| Recall@1 | 0.346 |
| Recall@5 | 0.582 |
| Recall@10 | 0.673 |
| MRR@10 | 0.447 |
| Median rank of correct doc | 3 |

### Improvement After Fine-tuning

| Metric | Baseline | Fine-tuned | Absolute Change | Relative Improvement |
|---|---:|---:|---:|---:|
| Recall@1 | 0.281 | 0.346 | +0.065 | +23.1% |
| Recall@5 | 0.464 | 0.582 | +0.118 | +25.4% |
| Recall@10 | 0.543 | 0.673 | +0.130 | +23.9% |
| MRR@10 | 0.359 | 0.447 | +0.088 | +24.5% |
| Median rank | 7 | 3 | -4 | 57.1% lower |

After fine-tuning, Qwen3-Embedding-0.6B also improved clearly. Recall@10 increased from 0.543 to 0.673, a relative improvement of 23.9%, and the median rank improved from 7 to 3.

## Overall Interpretation

Both embedding models improved after fine-tuning.

E5 Large had the larger relative gain, especially because its baseline performance was weaker. Its Recall@10 improved from 0.371 to 0.623, and its median rank improved from 42 to 4.

Qwen3-Embedding-0.6B started with a stronger baseline and still improved after fine-tuning. Its Recall@10 improved from 0.543 to 0.673, and its median rank improved from 7 to 3.

Overall, the results show that the retrieval fine-tuning pipeline successfully improved query-document matching performance for both embedding models.

## Large Artifacts Not Included

The following large files are intentionally excluded from GitHub:

- raw data folders
- generated `dataset/`
- generated `runs/`
- full model checkpoint folders
- `model.safetensors`
- training logs
- temporary files

The complete fine-tuned model artifacts are stored separately outside GitHub.
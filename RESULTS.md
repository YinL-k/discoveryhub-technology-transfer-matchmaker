# Results Summary

## Models Tested

This project tested two embedding-model settings:

1. Qwen3-Embedding-0.6B
   - Used for the earlier small embedding-model fine-tuning pipeline.
   - Main pipeline code is in `discovery_ft/`.

2. intfloat/multilingual-e5-large
   - Used for the later Anvil-based fine-tuning and evaluation run.
   - The full model checkpoint is not included in GitHub because it is too large.
   - E5 evaluation reports are included in `reports/`.

## Data Summary

| Item | Count |
|---|---:|
| Total usable records | 26,648 |
| AUTM / TTO records | 6,671 |
| ClinicalTrials.gov records | 9,977 |
| USPTO patent records | 10,000 |
| Document-side records | 16,671 |
| Query-side records | 9,977 |

## E5-Large Result

| Model | Setting | Recall@1 | Recall@5 | Recall@10 | MRR@10 | Median Rank |
|---|---|---:|---:|---:|---:|---:|
| intfloat/multilingual-e5-large | fine-tuned | 0.313 | 0.531 | 0.623 | 0.406 | 4 |

## Large Artifacts Not Included

The following large files are intentionally excluded from GitHub:

- raw data folders
- generated `dataset/`
- generated `runs/`
- full model checkpoint folder
- `model.safetensors`
- training logs
- temporary files

The complete E5 fine-tuned model was stored separately outside GitHub.

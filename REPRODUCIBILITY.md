# Reproducibility Guide

## Recommended execution order

The notebooks in `analysis/` should be interpreted in the following order:

1. `phase3_data_audit_split.ipynb`
2. `phase4a_answer_generation.ipynb`
3. `phase4b_evidence_filtering.ipynb`
4. `phase4c_grounding_faithfulness.ipynb`
5. `phase4d_task_specific_confidence.ipynb`
6. `phase4e_selective_answering.ipynb`
7. `phase5_locked_internal_test.ipynb`
8. `phase6_final_analysis.ipynb`
9. `reviewer_revision_grounding_analysis.ipynb`

The final notebook contains post-hoc reviewer analyses and was not part of the original
model-selection procedure.

## Dataset split

BioASQ14b questions were partitioned using duplicate-safe grouping with random seed 42.

The resulting split contained:

- training: 4,007 questions;
- validation: 862 questions;
- locked internal test: 860 questions.

Relevant files:

- `splits/bioasq14_internal_split_manifest.csv`
- `splits/split_integrity.json`
- `splits/document_overlap_across_splits.csv`

Raw question text is intentionally not redistributed in the public split manifest.

## Retrieval pipeline

The frozen retrieval sequence was:

    BM25 + MedCPT
          |
          v
    Reciprocal Rank Fusion
          |
          v
    Top-100 candidates
          |
          v
    MedCPT Cross-Encoder reranking

Retrieval metrics were evaluated at rank 10.

Generation-context depth was selected separately from retrieval depth.

## Evidence pruning

Three validation evidence conditions were compared:

1. Retrieved Top-10
2. Document Top-5
3. Sentence-filtered evidence

The sentence-filtered condition:

- started from the frozen Top-10 documents;
- deterministically split abstracts into sentences;
- discarded fragments shorter than 25 characters;
- removed normalized duplicate sentences within each question;
- scored question-sentence pairs using the MedCPT Cross-Encoder;
- retained at most two sentences per PubMed document;
- retained at most eight sentences overall;
- applied a fixed 1,800-token evidence budget;
- used Cross-Encoder logits for ranking only;
- did not use a relevance threshold.

Document Top-5 was selected on validation and frozen.

## Answer scoring

Factoid and list answer matching used a common normalization procedure:

- Unicode NFKC normalization;
- lowercase conversion;
- surrounding whitespace removal;
- underscore-to-space conversion;
- removal of punctuation except periods, hyphens, plus signs, and forward slashes;
- repeated whitespace collapse.

BioASQ synonym groups were preserved.

Factoid evaluation considered at most five ranked predictions.

List predictions were matched sequentially to previously unmatched gold synonym groups.

## Grounding

Claim-level grounding was computed from generated ideal answers.

For retrieved evidence, two-sentence overlapping windows were used for grounding analysis.
MedCPT Cross-Encoder relevance ranking was followed by biomedical NLI.

Grounding was treated as an evidence-support signal and not as a probability of clinical correctness.

For yes/no, factoid, and list tasks, benchmark task scores were derived from separate exact-answer
fields. Grounding-quality correlations for these tasks are therefore cross-output associations.

## Confidence modeling

Confidence models were fitted separately for:

- yes/no;
- factoid;
- list;
- summary.

Nested cross-fitting used 10 outer folds and 5 inner folds.

Raw Ridge predictions were used for ranking. Isotonic regression was used to estimate
task-specific expected benchmark scores.

Task eligibility was determined on validation and frozen before locked-test scoring.

## Selective answering

The primary policy applied selective answering only to summary questions.

The secondary policy applied selective answering to summary and factoid questions.

The within-task retention target was 80% for eligible tasks.

Yes/no and list questions were always answered.

Selective results should be interpreted as risk at fixed coverage rather than improvement
in full-benchmark performance.

## Locked-test protocol

The 860-question locked internal test was evaluated once after methodological decisions were frozen.

### Stage A

Only label-blind fields were available. The frozen pipeline produced:

- retrieval outputs;
- generated answers;
- grounding results;
- confidence predictions;
- selective-answering masks.

These artifacts were frozen before final scoring.

### Stage B

Gold fields were opened once and used to score the previously frozen outputs.

No model, retrieval configuration, prompt, grounding procedure, confidence model,
eligibility rule, or coverage rule was modified after label access.

## Reviewer analyses

Task-specific grounding-quality correlations were added in response to peer review.

The locked-test correlation analyses are exploratory and post-hoc. They did not influence the
original frozen pipeline or decision rules.

See the `reviewer_revision/` directory.

## Environment

The notebooks were developed in Google Colab and use the project root:

    /content/drive/MyDrive/MedicalNLP_RAG

Change this path when executing the notebooks in another environment.

`requirements.txt` lists the principal Python packages required by the workflow.
Exact accelerator, CUDA, and platform-specific package versions may depend on the
execution environment.

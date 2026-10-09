# A Multi-Dimensional Evaluation of Retrieval-Augmented Generation for Biomedical Question Answering

This repository contains the analysis code, derived per-question outputs, split identifiers,
and reproducibility records associated with the study:

**A Multi-Dimensional Evaluation of Retrieval-Augmented Generation for Biomedical Question Answering**

## Overview

The study evaluates multiple dimensions of a biomedical retrieval-augmented generation (RAG)
pipeline using BioASQ14b questions. The evaluated components include:

- lexical and dense biomedical retrieval;
- reciprocal rank fusion and Cross-Encoder reranking;
- evidence-conditioned answer generation;
- evidence pruning;
- claim-level grounding;
- task-specific confidence estimation;
- selective answering;
- one-time locked internal test evaluation.

The main pipeline uses BM25 and MedCPT retrieval, reciprocal rank fusion, MedCPT Cross-Encoder
reranking, Qwen3-4B-Instruct-2507 for answer generation, and biomedical NLI for claim-level
grounding.

## Repository structure

- `analysis/`: Final analysis notebooks used for the reported experiments.
- `splits/`: Duplicate-safe BioASQ14b split identifiers and integrity checks.
- `outputs/validation/`: Derived question-level validation outputs.
- `outputs/locked_test/`: Derived question-level outputs from the frozen locked internal test.
- `reviewer_revision/`: Additional task-specific grounding-quality analyses performed during peer review.
- `manifests/`: Freeze manifests and cryptographic records supporting the locked-test protocol.
- `data/`: Instructions for obtaining source data. Raw BioASQ and PubMed content is not redistributed.

## Data availability

BioASQ14b source data should be obtained through the official BioASQ infrastructure.
PubMed bibliographic records should be obtained from the National Center for Biotechnology
Information (NCBI).

Raw BioASQ questions, gold answers, and a redistributed copy of the PubMed corpus are not
included in this repository. Derived split identifiers, question-level metrics, model outputs,
grounding scores, confidence estimates, and analysis artifacts are provided.

See `data/README.md` for further details.

## Google Colab path convention

The notebooks were developed in Google Colab and assume the following project root:

    /content/drive/MyDrive/MedicalNLP_RAG

Users running the notebooks in a different environment should update the `ROOT`,
`PROJECT_ROOT`, or equivalent project-path variables defined near the beginning of each notebook.

The absolute Colab paths are retained intentionally to preserve the original analysis workflow.

## Reproducibility

See `REPRODUCIBILITY.md` for:

- the recommended execution order;
- the locked-test protocol;
- the distinction between validation-time development and final test evaluation;
- post-hoc reviewer analyses.

## Important evaluation scope

The PubMed retrieval benchmark is a benchmark-scoped closed corpus constructed from the
global union of PubMed identifiers linked to BioASQ14b questions. Retrieval results therefore
characterize ranking within this BioASQ-relevant candidate universe and should not be interpreted
as open-corpus retrieval over the full PubMed index.

The study-defined `MacroTaskScore` is the unweighted mean of:

- yes/no accuracy;
- factoid MRR;
- list F1;
- summary ROUGE-L.

It is an internal composite evaluation measure and is not an official BioASQ metric.

## Locked internal test

The final internal test contained 860 questions. Model-development and policy decisions were
completed before access to locked-test gold answers.

The locked evaluation used two stages:

1. **Stage A:** label-blind inference and freezing of predictions, grounding outputs,
   confidence predictions, and selective-answering masks.
2. **Stage B:** one-time scoring of the frozen outputs after access to gold fields.

The locked test is an internal hold-out and should not be interpreted as external validation.

## Peer-review analyses

The `reviewer_revision/` directory contains additional grounding-quality correlation analyses
performed in response to peer review. These analyses were post-hoc and did not alter any
frozen model, retrieval configuration, confidence model, task-eligibility decision, or
selective-answering policy.

## License and third-party data

This repository contains original analysis code and derived outputs. Third-party datasets and
model weights remain subject to their original licenses and terms of use.

Users are responsible for obtaining BioASQ, PubMed, Qwen, MedCPT, PubMedBERT/MedNLI,
and other third-party resources from their respective official sources.

## Author

Tülay Turan  
Department of Computer Engineering  
Faculty of Engineering and Architecture  
Burdur Mehmet Akif Ersoy University  
Burdur, Türkiye

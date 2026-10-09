# Source Data

Raw third-party data are not redistributed in this repository.

## BioASQ14b

The experiments use the BioASQ14b biomedical question-answering resource.

Users should obtain the source data through the official BioASQ challenge infrastructure.

The repository provides only derived identifiers and analysis artifacts required to reproduce
the study-specific partitioning and evaluation.

## PubMed

The retrieval corpus was constructed from PubMed identifiers linked to BioASQ14b questions.

The repository does not redistribute PubMed abstracts or full bibliographic records.
Users should obtain the corresponding records through NCBI/PubMed services.

## Split reconstruction

Use `../splits/bioasq14_internal_split_manifest.csv` to map BioASQ question identifiers
to the study-specific train, validation, and locked-test partitions.

Question text and normalized question text were removed from the public split manifest.

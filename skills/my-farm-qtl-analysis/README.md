# My Farm QTL Analysis

My Farm QTL Analysis is the genetics and quantitative-analysis skill pack for this repo. It covers GWAS, eQTL mapping, classical QTL workflows, kinship, population structure, genomic prediction, and the reporting patterns needed to turn marker-level results into breeding insight.

Use this skill when the problem is trait-genetics analysis, association mapping, or breeding-genomics interpretation.

## What This Skill Does

- Routes each QTL-style problem to the right analysis family instead of forcing one tool to do everything.
- Combines the strongest open-source tools for GWAS, eQTLs, classical QTL, kinship, PCA, and genomic prediction.
- Provides worked example modules for common and advanced breeding-genomics tasks.
- Supports both command-line execution and reproducible workflow framing.
- Bridges raw genotype/phenotype data to biological interpretation and breeding decisions.

## Tooling Model

```mermaid
flowchart TD
    accTitle: QTL Analysis Tool Routing
    accDescr: Overview of how the QTL analysis skill routes a genetics question into the right analysis family and then into the most appropriate open-source tool chain.

    question([🧬 Genetics question]) --> choose{Choose analysis family}
    choose --> gwas[GWAS]
    choose --> eqtl[eQTL]
    choose --> classical[Classical QTL]
    choose --> structure[Structure or kinship]
    choose --> prediction[Genomic prediction]
    gwas --> gemma[⚙️ GEMMA or PLINK]
    eqtl --> tensor[⚡ tensorQTL]
    classical --> rqtl[📍 R/qtl2]
    structure --> pca[📊 PCA and kinship tools]
    prediction --> gp[📈 GBLUP and marker workflows]
    gemma --> results([✅ Plots, reports, and decisions])
    tensor --> results
    rqtl --> results
    pca --> results
    gp --> results

    classDef primary fill:#dbeafe,stroke:#2563eb,stroke-width:2px,color:#1e3a5f
    classDef tool fill:#fef3c7,stroke:#d97706,stroke-width:2px,color:#78350f
    classDef outcome fill:#dcfce7,stroke:#16a34a,stroke-width:2px,color:#14532d

    class question,choose primary
    class gemma,tensor,rqtl,pca,gp tool
    class results outcome
```

The point of this skill is not one monolithic tool. It is the orchestration layer that chooses the right open-source method for the job.

## Core Capability Areas

| Area                 | What it covers                                 | Typical output                                |
| -------------------- | ---------------------------------------------- | --------------------------------------------- |
| GWAS                 | Linear mixed model and GLM association studies | Manhattan plots, QQ plots, hit tables         |
| eQTL                 | cis/trans expression QTL mapping               | gene-variant association results              |
| Classical QTL        | Experimental-cross QTL workflows               | LOD scans and linkage-based QTL calls         |
| Population Structure | PCA, admixture, kinship, relatedness           | structure plots and kinship matrices          |
| Genomic Prediction   | GBLUP and breeding prediction workflows        | prediction accuracy and selection guidance    |
| QC and Annotation    | SNP filtering, validation, and annotation      | cleaned genotype inputs and annotated markers |

## End-to-End Analysis Flow

```mermaid
flowchart LR
    accTitle: End-To-End QTL Workflow
    accDescr: Typical progression from genotype and phenotype inputs through QC, structure checks, analysis, visualization, and breeding interpretation.

    data([🧪 Genotype and phenotype data]) --> qc[QC and format checks]
    qc --> structure_step[Population structure and kinship]
    structure_step --> analysis[Run GWAS, eQTL, or QTL]
    analysis --> visuals[📊 Produce plots and summaries]
    visuals --> decisions([✅ Interpret results for breeding action])

    classDef primary fill:#dbeafe,stroke:#2563eb,stroke-width:2px,color:#1e3a5f
    classDef process fill:#fef3c7,stroke:#d97706,stroke-width:2px,color:#78350f
    classDef outcome fill:#dcfce7,stroke:#16a34a,stroke-width:2px,color:#14532d

    class data primary
    class qc,structure_step,analysis,visuals process
    class decisions outcome
```

## Example Families Included

- Core GWAS and structure work: `examples/gwas-lmm/`, `examples/gwas-glm/`, `examples/population-structure/`, `examples/admixture/`
- Kinship and relatedness: `examples/pedigree-kinship/`, `examples/genomic-nrm/`, `examples/genetic-similarity/`
- Prediction and selection: `examples/genomic-prediction/`, `examples/marker-selection/`, `examples/blup/`, `examples/backcross-selection/`
- Advanced association work: `examples/multi-trait-gwas/`, `examples/gxe-gwas/`, `examples/covariate-gwas/`, `examples/rare-variant-tests/`
- Reporting and support: `examples/analysis-report/`, `examples/sample-qc/`, `examples/snp-annotation/`

## Why It Matters In This Repo

```mermaid
flowchart TD
    accTitle: QTL Skill Role In The Repo
    accDescr: Diagram showing how breeding and field data enter the QTL analysis skill and produce marker discovery, prediction, reporting, and breeding program decisions.

    breeding_data([🌱 Breeding and field data]) --> qtl_skill[🧬 QTL analysis skill]
    qtl_skill --> discovery[Marker-trait discovery]
    qtl_skill --> prediction_output[Genomic prediction]
    qtl_skill --> reporting[📊 Visual and statistical reporting]
    discovery --> breeding_programs([✅ Breeding program decisions])

    classDef primary fill:#dbeafe,stroke:#2563eb,stroke-width:2px,color:#1e3a5f
    classDef support fill:#ecfccb,stroke:#65a30d,stroke-width:2px,color:#365314
    classDef outcome fill:#f3e8ff,stroke:#9333ea,stroke-width:2px,color:#581c87

    class breeding_data,qtl_skill primary
    class discovery,prediction_output,reporting support
    class breeding_programs outcome
```

This skill is the analysis-heavy counterpart to breeding trial management. Trial management helps run the breeding program; QTL analysis helps explain the genetics and rank what to do next.

## Start Here

- Main entrypoint: [`SKILL.md`](SKILL.md)
- Tool selection guide in `SKILL.md`
- Full worked examples under [`examples/`](examples/)
- Common baselines: `examples/gwas-lmm/`, `examples/eqtl-cis/`, `examples/classical-qtl/`, `examples/genomic-prediction/`

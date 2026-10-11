# From Cells to Context: Single-Cell Immune Profiling → Spatial Tissue Mapping → CNN Tissue Classification

## Project Summary

This project explores immune cell biology across three complementary lenses: single-cell transcriptomics, spatial transcriptomics, and tissue image classification. Rather than three disconnected exercises, it's framed as one pipeline — starting from "what cell types exist" (scRNA-seq), to "where do they live in tissue" (spatial transcriptomics), to "can tissue structure be recognized from image morphology alone, independent of RNA" (CNN). The project was developed in preparation for an Informatics internship conversation at Element Biosciences, informed by an early conversation with a member of their team who flagged these areas — spatial data, 10x data, cell typing, spatial data objects, and CNNs — as relevant to the team's work.

## Goals

- Build real, working analysis pipelines in each area rather than surface-level familiarity with buzzwords
- Demonstrate the same analytical logic (QC → normalization → dimensionality reduction → clustering) applies across modalities, adapted to each one's specific structure
- Be explicit throughout about what's rigorous analysis vs. deliberately scoped demonstration
- Produce a coherent narrative that can be walked through in a short conversation, not three unrelated repos

## Pipeline

1. **[`01_scrna_pbmc/`](./01_scrna_pbmc/)** — Single-cell RNA-seq analysis of PBMC data: QC → normalization → PCA → UMAP → Leiden clustering → marker-gene-based cell-type annotation → logistic regression classifier. Establishes cell-type identity from transcription alone, with no spatial context.

2. **[`02_spatial_transcriptomics/`](./02_spatial_transcriptomics/)** — 10x Visium spatial transcriptomics on human lymph node tissue. Same core analytical logic as the PBMC project (QC, normalization, clustering), extended to data where each unit is a multi-cell spot with a physical (x, y) tissue location. Tests whether transcriptional clustering corresponds to real spatial tissue organization.

3. **[`03_tissue_cnn/`](./03_tissue_cnn/)** *(in progress)* — A PyTorch CNN classifying tissue image patches by morphology, independent of any RNA measurement. The next layer down: if spatial transcriptomics shows gene expression organizes into physical tissue structure, can that same structure eventually be recognized directly from how tissue *looks*, without sequencing at all?

## Conclusions So Far

**PBMC (single-cell):** [1–2 sentences on your actual PBMC findings/conclusions — fill in from that project]

**Spatial transcriptomics (lymph node):**
- Loaded a public 10x Visium human lymph node dataset (4,035 spots × 36,601 genes). QC thresholds were chosen empirically via a sorted-count "knee plot" rather than reused default cutoffs, retaining 3,995 spots; mitochondrial content was uniformly low across the tissue, so no separate mito filter was needed.
- Standard normalization, HVG selection, PCA, and Leiden clustering (resolution=1.0) produced 9 transcriptionally distinct clusters.
- Critically, these clusters were **not randomly scattered** when overlaid on the tissue image — they formed spatially coherent, patch-like regions consistent with known lymph node anatomy (e.g. germinal centers, T-cell zones). This is the core finding of the subproject: transcriptional identity and physical tissue organization are not independent — they correspond.
- Moran's I spatial autocorrelation, run independently of cluster labels, identified genes with strong spatial structure. A substantial fraction of these genes (36 of the top cluster-defining markers) also appeared among the top spatially autocorrelated genes — two independent methods converging on overlapping genes, which strengthens confidence that both are detecting real biological organization rather than noise.
- This contrasts directly with the PBMC project: dissociated single-cell data can identify *what* cell types exist, but the dissociation process destroys any record of physical organization. Spatial transcriptomics preserves exactly the information scRNA-seq loses.

## What's Real Analysis vs. Demonstration

- PBMC and spatial transcriptomics subprojects are full, real analyses on public data, with QC decisions justified from the data itself rather than copied defaults.
- The CNN subproject (in progress) is a learning-scoped exercise on a small public dataset — not a production-grade or state-of-the-art model.
- Not included: full cell-type deconvolution (Tangram), RNA+protein multimodal integration, or custom segmentation models. These are noted as future directions, not implemented here.

## Next Steps (CNN)

The current focus is `03_tissue_cnn/`: building a small CNN in PyTorch to classify tissue image patches by morphology. Planned approach:
- Start with a from-scratch CNN on a simple dataset to build intuition for convolution, pooling, and feature maps
- Move to a small public histology-patch dataset, train a baseline CNN, then compare against a transfer-learning approach (e.g. fine-tuned ResNet18)
- Evaluate with a proper train/val/test split and confusion matrix, with honest discussion of failure modes rather than overclaiming performance

## Future Directions

- Tangram-based or marker-based cell-type deconvolution applied to the spatial data
- RNA+protein (CITE-seq-style) multimodal integration
- Image segmentation (e.g. Cellpose-style) as a more granular alternative to patch classification
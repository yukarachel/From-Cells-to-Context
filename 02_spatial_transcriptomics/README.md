# Spatial Transcriptomics Analysis on 10x Visium Lymph Node Dataset

## What is spatial transcriptomics / what is a Visium spot
Spatial transcriptomics allows us to understand RNA expression from cells in tissue with spatial context. While scRNA-seq data can give us expression information from transcripts per cell, we lack any context about where in tissue they lie. Knowing which part of tissue shows expression from which genes can help solve more complex biological problems, like knowing how a T-cell behaves near a tumor cell vs far away from one. Spatial transcriptomics is analyzing the transcriptome while utilizing its spatial information in tissue. 

10x Visium spots are 55 um in diameter with a 100um center-to-center distance between them. They can capture between 1-10 cells on average, which depends on the tissue type and cell size. In this project, I investigated the expression of spots, which could contain multiple cells within each spot. These spots and their locations in tissue are analyzed as well, to extract spatial patterns. I used two independent methods to conclude that there are several marker genes that can expose structural information of tissue. 

## Dataset
- Source: squidpy built-in dataset, V1_Human_Lymph_Node (10x Genomics public data)
- 4035 spots × 36,601 genes (raw)

## QC
- Filtered spots with low expression. The cutoff determined by the sorted-counts knee plot was around rank 4000 (sorted total counts in descending order). The "low expression" was around 3100 total counts. I did not use a default threshold. 3995 spots retained.
- Filtered genes detected in <3 spots
- Mitochondrial % was uniformly low (max 5.4%); no additional mito filtering applied

## Clustering
- Normalized (target_sum=1e4) + log1p, 2000 HVGs, PCA (50 components), Leiden (resolution=1.0) → 10 clusters

## Spatial findings
- Clusters form spatially coherent oval regions on tissue, not random speckling, which suggests clusters correspond to real anatomical structures (ex. germinal centers, T-cell zones)
- Marker genes (CD3E, CD19, CD68) show spatial enrichment aligned with cluster boundaries
- Moran's I spatial autocorrelation identified IGKC, IGHG4, IGHG1, CCL21, FDCSP, MT-CO1, IGHG2, MT-CO3 as spatially variable, which overlaps with cluster-defining marker genes

## Figures
The knee plot used to determine the cutoff for low transcription spots. 
![alt text](image-3.png)

After Leiden clustering, I used UMAP to visualize all 10 clusters in 2D. 
![alt text](image.png)

Based on known marker genes for T-cells, B-cells, and macrophages, I created a spatial plot showing the expression of CD3E, CD19, and CD68 on the tissue. This does correlate to the leiden clustering above. 
![alt text](image-2.png)

I looked into other genes that could uncover spatial data using Moran's I. These genes are most informative in showing spatial context. 
![alt text](image-1.png)

Finally, I showed the tissue map of the most indicative genes found by both the spatial Moran's I method and transcription based clustering method. 
![alt text](image-4.png)

# Tissue Classification CNN

## Goal
Classify histology image patches by tissue/cell type using image morphology alone, independent of any gene expression data. This is the next layer down from the spatial transcriptomics project, where I analyzed lymph node spatial expression data to determine groups of cell populations using RNA and spatial data. Now, I want to see if cell typing is possible using images and a CNN. 

## Dataset
PathMNIST (MedMNIST): 9-class colorectal histology patches, 28x28 RGB.

## Architecture
Small 2-conv-layer CNN trained from scratch (same base architecture validated on FashionMNIST first, see cnn_training.ipynb).

## Results
- Train accuracy: 80.1% (5 epochs)
- Test accuracy: 67.3%
- ~13-point train/test gap suggests mild overfitting

## Key findings
- **Cancer detection (colorectal adenocarcinoma epithelium):** high precision (91%) but low recall (52%). The model flags cancer, and it's usually right, but it misses nearly half of true cancer cases (false negatives), the more clinically concerning failure.
- **Lymphocytes were poorly classified by morphology alone (21% recall)**, despite being clearly identifiable via gene markers (CD3E, CD19) in the spatial transcriptomics subproject. Multimodal approaches (combining RNA expression and imaging) likely could outperform either alone.
- **Debris** had the lowest precision (27%). The model over-predicts this class broadly, a different failure pattern (over-flagging) than the cancer under-flagging.

## Limitations
- No data augmentation
- Simple from-scratch architecture, no transfer learning
- Small dataset, no hyperparameter tuning

## Future directions
- Transfer learning (ResNet18 fine-tuning) to address the train/test gap
- Data augmentation
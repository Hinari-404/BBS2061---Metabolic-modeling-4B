# BBS2061---Metabolic-modeling-4B

## Usage Guide

### Introduction to genome scale metabolic modeling
Genome scale metabolic models (GEMs) utilise genomic, proteomic and metabolomic data and combines them into one model.
GEMs allow us to predict flux changes in our model depending on our objective (i.e. maximal ATP production), and constraints (i.e. a certain amount of O2).
The resulting information can be used to determine main and side reactions, study metabolic switches, simulate metabolism under different conditions, and more!\
Below, we will give a step by step guide on how to use our repository to simulate aerobic and anaerobic conditions, as well as how transcriptome datasets can be used to modify our GEM. 
For more details, read our paper provided on canvas (not added here for privacy).

## Setup

All software was tested and used in MATLAB version RB2021b. You can download MATLAB _[here](https://matlab.mathworks.com)._\
Additionally download the following tools:
+ [Recon 2.2](https://www.ebi.ac.uk/biomodels/MODEL1603150001)
+ [COBRAToolbox](https://opencobra.github.io/cobratoolbox/stable/installation.html)\
! Make sure you run CobraToolbox before running any additional matlab files provided. 


## Step 1: Simulating metabolism in aerobic and anaerobic conditions

Download and open file [FBA_normal.mlx](/FBA_normal.mlx). Running this code will simulate metabolic flux under anaerobic and aerobic conditions.

You can alter the following lines of code based on your needs:
```
modelaerobic = changeObjective(modelaerobic, 'DM_atp_c_');
```
Change 'reaction' to reaction of choice, i.e. 'DM_atp_c_' The model will maximise flux through this reaction.
```
draw_by_met(model, {'chol[c]'}, 'true', 1, 'struc', {''}, FBAaerobic.v);
```
Change metabolite to visualise map of its first degree interactions

## Step 2: Altering the model based on a transcriptomic dataset

Download and open file [FBA_cancer.mlx](/FBA_cancer.mlx). Running this code will remove genes satisfying a certain condition in your transcriptomic dataset, and simulate metabolic flux under aerobic and anaerobic conditions.
We used data from the [TCGA Breast Cancer Project](https://www.cancerimagingarchive.net/collection/tcga-brca/), which was modified to select only gene expression profiles from 20 Caucasian female patients aged 60–75 years who were all diagnosed with stage IA breast cancer (see 'enter file name')

You can additionally change the following lines of code:
```
index = string(table2array(gxData(:,3))) == '0';
```
The condition " == 0 " can be changed to a different criterion, such as selecting only gene expression > 0,  < 0.1, etc. 
Additional alterations can be made in the same way as described in step 1.

## Step 3: 'Knocking out' reactions of choice from a cell cancer model

Download and open file [FBA_cancer_withAdditionalGeneDeletion_2024.mlx](/FBA_cancer_withAdditionalGeneDeletion_2024.mlx). Running this code will additionally remove genes from the model based on an HGNC (gene) identifier.
In our research, we used the following genes, corresponding to 3 different reactions: 
```
genesToRemoveAdd = genesToRemove;
genesToRemoveAdd{end + 1} = 'HGNC:14025';
genesToRemoveAdd{end + 1} = 'HGNC:10966';
genesToRemoveAdd{end + 1} = 'HGNC:10969';
genesToRemoveAdd{end + 1} = 'HGNC:1937';
genesToRemoveAdd{end + 1} = 'HGNC:1938';
[modelCadd, hasEffect, constrRxnNames, deletedGenes] =... 
    deleteModelGenes(modelRPMI, genesToRemoveAdd);
```
The list of genes can be altered and ammended by duplicating the line of code.\
Additionally, the specific flux of any given reaction in either the cancer model, or breast cancer model with additional gene deletions can be determined using the following script:
```
rxnID = 'CHOLt4';
rxnIndex = find(strcmp(model.rxns, rxnID));
flux_cancer = FBAaerobicC.v(rxnIndex)
flux_Cadd = FBAaerobicCadd.v(rxnIndex)
```
The rxnID corresponds to the reaction name (i.e. 'CHOLt4').\

**How do I find the correct genes for my purpose?**

COBRA Toolbox offers [a variety of commands](https://opencobra.github.io/cobratoolbox/stable/modules/analysis/exploration/index.html#src.analysis.exploration.findGenesFromMets) which make traversing the GEM and its different types of data easier. These may include:
```
findGenesFromMets(model, 'metabolite')
findGenesFromRxns(model, 'reaction')
```
Finds all genes responsible for reactions in one or more metabolites, or all genes involved in one reaction.

## Thanks for reading! :>











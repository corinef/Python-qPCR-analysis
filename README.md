# Python qPCR analysis for Masters' Thesis
**Corine Faehn**

Full analysis of *F. vesca* circadian clock gene expression in differential photoperiod treatments via RT-qPCR analysis 

# Overview
Quantitative real-time PCR is an important technique in analyzing real-time gene expression analysis. Gene expression analysis software is expensive, and calculation of relative expression of target gene using a reference gene is relatively straight-forward. This document includes the analysis used in this master thesis to relatively quantify gene expression of qPCR via a Python script. 

## Background information on qPCR data
Cq values represent the number of PCR cycles needed to observe a certain threshold of fluorescence for each sample. DNA sequences are doubled each cycle, so Cq values are on a log2 scale (1 cycle = 2x original sequence abundance, 2 cycles = 4x original sequence abundance), therefore higher Cq values corresponds to lower DNA expression levels. 

To normalize the Cq data, target gene Cq values can be compared to an internal control/reference gene Cq value in each sample. 
This gives a change in Cq value (ΔCq) for each gene, then to convert ΔCq values from a log2 to a linear scale, we take 2 to the power of -ΔCq.

the 2<sup>-ΔCq</sup> method is appropriate where: 
    deltaCq =  (CQ gene of interest - CQ internal control).


## Necessary Packages to install:

jupyterthemes
scikit_posthocs
researchpy

# Content 
Each run is an individual [Plate file](https://github.com/corinef/thesis-Python-qPCR/tree/main/Plate%20files) (1-62) representing one gene tested per biological replicate. *Download* the [zipped plates file](https://github.com/corinef/Python-qPCR-analysis/blob/main/Plate%20files/plate_files.zip) and script for full use. 


Runs grouped together by biological replicate (BR). 

1. Each BR dataframe includes: 
    * clean up:
        * Drop unused columns
        * NaN values renamed
            * NTC (no template control) for samples
            * 0 for Cq values
        * Fix plate mistakes 
            * misnamed targets, well re-runs
            
2. Group all data in one dataframe
    * Pull out IRC's (validated later)
    * Set column names for Sample ID's

3. Check data integrity
    * Check NTC's greater than 0
        * Validate further by melt curves
    * Drop NTC's
    
4. Plot failed wells
    * Identify samples where reference gene failed (nothing to compare to, inhibits downstream analysis)
    * Drop failed wells (where Cq = 0)
    
5. Identify outliers using reference genes
    * IQR method
    * Add samples where ref gene failed
    * Update dataframe
   
6. Check reference gene stabiity
    * Plot Cq values grouped by clone and treatment
    * Statistics
        * Ordinary Least Squares (OLS)
        * Post-Hoc Tukey HSD test
            * To identify which groups are significantly different for each ref gene
            
7. Analyze target genes
    * Plot mean Cq values
    * Calculate 2<sup>-ΔCq
    
        * ΔCq = Cq (target gene) - Cq (eometric mean of reference genes)
        * Take 2<sup>-ΔCq
    * Plot target genes
    * Independent Students T-tests for hypotheses
        * Independent variable: Treatment (4 levels) Zeitgeber (13 levels)
        * Dependent variable: 2<sup>-ΔCqs



    
<!-- This content will not appear in the rendered Markdown   

Unused for analysis:
    Inter-Run-Calibrator (IRC) dataframe includes:
    * Merge of all IRC data
    * Grouped by plate
    * Fit Ordinary Least Squares (OLS) regression model using an estimation method
        * estimate relationship between plate and Cq values  
-->

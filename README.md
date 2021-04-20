# thesis-Python-qPCR
**Corine Faehn**

Full analysis of *F. vesca* circadian clock gene expression in differential photoperiod treatments via RT-qPCR

# Overview
Quantitative real-time PCR is an important technique in analyzing real-time gene expression analysis. Gene expression analysis software is expensive, and calculation of relative expression of target gene using a reference gene is relatively straight-forward. This document includes the methods used in this master thesis to relatively quantify gene expression of qPCR. 


# Necessary Packages:

## Libraries
```
import pandas as pd
from pathlib import Path
import numpy as np
import os
from math import isnan
```
## Plot libraries
```
import matplotlib as mpl
import matplotlib.pyplot as pp
import seaborn as sb
```
## Set custom design for plots
```
%matplotlib inline
IPython_default = pp.rcParams.copy()
from matplotlib import cycler
colors = cycler('color',
                ['#EE6666', '#66EEEE', '#690202',
                 '#022869', '#9988DD', '#88BB44'])
    ## color order: bright red, bright blue, dark red, dark blue, purple, green
mpl.rcParams['font.family'] = 'helvetica'
params = {'legend.fontsize': 20,
          'figure.figsize': (16, 8),
         'axes.labelsize': 24,
         'axes.titlesize': 40,
         'xtick.labelsize':20,
         'ytick.labelsize':20}
mpl.rcParams.update(params)
pp.rc('axes', facecolor='#E6E6E6', edgecolor='none',
       axisbelow=True, grid=True, prop_cycle=colors)
pp.rc('grid', color='w', linestyle='solid')
pp.rc('xtick', direction='out', color='black')
pp.rc('ytick', direction='out', color='black')
pp.rc('patch', edgecolor='#E6E6E6')
pp.rc('lines', linewidth=2)
```
## Statistics libraries
```
from scipy import stats
import researchpy as rp
import statsmodels.api as sm
from statsmodels.formula.api import ols
from statsmodels.stats.multicomp import pairwise_tukeyhsd
import scikit_posthocs as sp
```

# Content 
Each run is an individual CSV file (1-32) representing one gene tested per biological replicate. Runs were grouped together by biological replicate (BR). 

1. Each BR dataframe includes: 
    * clean up: 
        * unneccessary columns dropped
        * NaN's renamed to NTC or IRC
        * Cq's of 0 dropped
        * High samples removed (Cq values (>30) for the majority of genes tested)
    * pivot table
        * comparative Cq method 

Omitted samples:
    From cDNA synthesis: 
        N2:  1A_2 , 4B_3, 11C_2 , 10A_3
        I1:  2B_2 , 10B_2    
    From qPCR analysis due to high samples: 
        N2: 5A_1
        I1: 8A_1, 11B_1, 13C_1, 13D_1, 1A_2, 1B_2, 7B_2, 7B_3, 13B_3


Cq values represent the number of PCR cycles needed to observe a certain threshold of fluorescence for each sample. DNA sequences are doubled each cycle, so Cq values are on a log2 scale (1 cycle = 2x original sequence abundance, 2 cycles = 4x original sequence abundance), therefore higher Cq values corresponds to lower DNA expression levels. 

To normalize the Cq data, target gene Cq values can be compared to an internal control/reference gene Cq value in each sample. 
This gives a change in Cq value (ΔCq) for each gene, then to convert ΔCq values from a log2 to a linear scale, we take 2 to the power of -ΔCq.

the 2^-ΔCq method is appropriate where: 
    deltaCq =  (CQ gene of interest - CQ internal control).


2. Inter-Run-Calibrator (IRC) dataframe includes:
    * Grouped by plate
    * Fit Ordinary Least Squares (OLS) regression model using an estimation method
        * estimate relationship between plate and Cq values  

4. Verify reference gene expression
    * Compare reference gene expression for each clone to identify if GAPDH or MSI1 change expression under treatment. 
    * One-Way ANOVA test
        1: Fit OLS model
            * estimate relationship between treatment and Cq values
        2: Pass fitted model into ANOVA method to produce ANOVA table
            * Check that data does not violate assumption of normally distributed residuals, or homogenity of variance.
            
5. Analyze Target Genes
    * 

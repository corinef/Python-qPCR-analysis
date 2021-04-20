# thesis-Python-qPCR
**Corine Faehn**

Full analysis of *F. vesca* circadian clock gene expression in differential photoperiod treatments via RT-qPCR


Each run is an individual CSV file (1-32) representing one gene tested per biological replicate. Runs were grouped together by biological replicate and concatenated to new dataframes. Each new dataframe was then cleaned up: unneccessary columns dropped, NaN's renamed to NTC or IRC, Cq's of 0 dropped, and abnormal samples removed from each dataframe. Abnormal samples had high Cq values (>30) for the majority of genes tested. Then each dataframe was converted into a pivot table to perform comparative Cq method. 

Omitted samples from cDNA synthesis: 

    N2:  1A_2 , 4B_3, 11C_2 , 10A_3
    I1:  2B_2 , 10B_2
    
Omitted samples from qPCR analysis: 

    N2: 5A_1
    I1: 8A_1, 11B_1, 13C_1, 13D_1, 1A_2, 1B_2, 7B_2, 7B_3, 13B_3


Cq values represent the number of PCR cycles needed to observe a certain threshold of fluorescence for each sample. DNA sequences are doubled each cycle, so Cq values are on a log2 scale (1 cycle = 2x original sequence abundance, 2 cycles = 4x original sequence abundance), therefore higher Cq values corresponds to lower DNA expression levels. 

To normalize the Cq data, target gene Cq values can be compared to an internal control gene Cq value in each sample. This gives a change in Cq value (ΔCq) for each gene, then to convert ΔCq values from a log2 to a linear scale, we take 2 to the power of -ΔCq.

the 2^-ΔCq method is appropriate where: 

deltaCq =  (CQ gene of interest - CQ internal control).


The deltaCq method using a reference gene is a variation of the Livak method that is simpler to perform and gives essentially the same results. In contrast to the deltaCq values obtained with relative quantification normalized against a unit mass, this method uses the difference between reference and target Cq values for each sample. Despite the simplicity of the approach, it is indeed normalized expression. The expression level of the reference gene is taken into account. The key difference in the results is that the expression value of the calibrator sample is not 1.0. If the resulting expression values obtained in this method are divided by the expression value of a chosen calibrator, the results of this calculation are exactly the same as those obtained with the Livak method:



IRC's were grouped and put into their own dataframe for further analysis. 

自己的单细胞数据加测了TCR/BCR，想和公共数据集的单细胞数据合并分析，但是，公共数据集的单细胞数据没有加测TCR/BCR，这样肯定是不能直接合并分析。
***
![image](https://github.com/user-attachments/assets/7788e09f-604b-4dbf-88d7-a0aa46374459)

这篇文章，提供了一个可行的思路

## 文献解决方案

使用正则表达式分别去除BCR/TCR基因

- BCR-genes were removed from the count data using regular expression commands with the following patterns: ‘IG[HKL]V’, ‘IG[KL]J’, ‘IG[KL]C’, ‘IGH[ADEGM]’.

- TCR-genes were removed from the count data using a regular expression command with the following pattern:’^TR[ABDG][VJC]’. A cluster containing cells positive for CD3E, CD19 and CD14 was discarded.

## 文献主要结论

文章中作者解决的问题是BCR/TCR会对聚类结果有影响，而不是我最开始提出的问题。😂误打误撞了

- the genes that encode B-cell antigen receptors interfere with the process of unsupervised clustering, as well as the down-stream analyses of these cells.

- the genes that encode T-cell receptors interfere with the unsupervised clustering of these cells.

- This interference is likely due to the high frequencies of B- and T-cell receptor genes among the genes that account for most of the variance in each of the PCs used for the clustering.

- The effects of the B-cell and T-cell receptor genes are abrogated upon their exclusion before clustering is undertaken.


并且还提供了代码

> Code availability
All custom codes used for data processing and computational analyses are available under project BCR_TCR_Interference at https://github.com/MartenssonLab/.


单细胞转录组测序 (scRNA-seq) 数据在本质上是一组细胞的表达谱，对于每一个细胞中的每个基因，我们会得到该基因在细胞中mRNA分子的计数。这反映了基因的“表达”或“活跃”程度。

像所有实际应用技术一样，原始数据可能会受到技术伪影的影响（例如将两个细胞的分子计入一个表达谱中、计算来自破裂细胞的分子、仅计算来自细胞核的分子等）。因此，需要对原始数据进行修剪，以排除这些伪影。

当前的scRNA-seq技术数据非常稀疏（通常只有不到10%的RNA分子被计数）。这在原始信号的基础上引入了较大的抽样方差，而原始信号本身也存在显著的固有生物学噪声。

> 在进行单细胞测序时，由于技术原因，每个细胞基本上只能检测到2000多个细胞，导致大量0值的出现

因此，分析scRNA-seq数据需要对大量的表达谱进行处理。传统的分析方式是通过使用各种方法直接对细胞进行聚类来完成的。

相比之下，Metacell方法将“相同”生物学状态的表达谱分组为具有“相同”生物学状态的细胞组，并使用最少的表达谱数量来计算稳健的统计量（特别是平均基因表达）。每一个这样的细胞组就是一个“metacell”。

通过将相同状态的细胞表达谱相加，每个metacell大大降低了抽样方差，并提供了更稳健的转录状态估计。需要注意的是，metacell并不是一个细胞类型（多个metacell可能属于相同的“类型”，甚至具有相同的“状态”，如果数据对这种状态进行了足够的过采样）。此外，metacell也不是细胞状态的参数模型。它只是对某种细胞状态的更稳健的描述。

因此，metacell可以进一步像细胞一样进行分析，使用额外的方法对细胞类型进行分类、检测细胞轨迹和/或谱系、构建细胞行为的参数模型等。将metacell作为这些分析技术的输入应能同时受益于更加稳健、噪声更小的输入；并且在处理大规模数据（例如分析数百万个单细胞）时，由于需要分析的细胞数量减少了约100倍，因此效率更高。

## Metacell方法示意

![image](https://github.com/user-attachments/assets/04d8684a-1590-4d13-b6f9-654d168c4527)

读入expression matrix之后，首先选择高变异gene，利用这些gene，计算细胞与细胞之间的距离，对细胞进行聚类，聚类结果用K-nn graph显示。然后通过“subsample，计算距离，聚类”这一过程的循环（比如1000次），得到了1000张k-nn graph。然后根据这1000张k-nn graph，细胞聚类的稳定性，refine cell之间是否存在可信的edge。得到高可信的graph之后，在对graph进行切分，细分成sub-graph，那些不归于任何一个sub-graph、零散分布的cell，被判定为outlier cell，可能是doublet，细胞破损等原因造成的。最后每一个subgraph对应一个metacell，metacell内部包含的cells被认为是homogeneous的。不同的metacell之间可能是不同cell type，也可能是用一个cell type不同的cell type，总而言之，就是metacell之间是异质的。同时，作者利用几何平均值等数据处理方法，整合同一个metacell中所有cells的expression profile，给每一个metacell一个gene expression profile/vector 作为这个metacell的footprint.

---
为了避免理解产生变差，附上英文原文

Naively, scRNA_seq data is a set of cell profiles, where for each one, for each gene, we get a count of the mRNA molecules that existed in the cell for that gene. This serves as an indicator of how "expressed" or "active" the gene is.

As in any real world technology, the raw data may suffer from technical artifacts (counting the molecules of two cells in one profile, counting the molecules from a ruptured cells, counting only the molecules from the cell nucleus, etc.). This requires pruning the raw data to exclude such artifacts.

The current technology scRNA-seq data is also very sparse (typically <<10% the RNA molecules are counted). This introduces large sampling variance on top of the original signal, which itself contains significant inherent biological noise.

Analyzing scRNA-seq data therefore requires processing the profiles in bulk. Classically, this has been done by directly clustering the cells using various methods.

In contrast, the metacell approach groups together profiles of the "same" biological state into groups of cells of the "same" biological state, with the minimal number of profiles needed for computing robust statistics (in particular, mean gene expression). Each such group is a single "metacell".

By summing profiles of cells of the "same" state together, each metacell greatly reduces the sampling variance, and provides a more robust estimation of the transcription state. Note a metacell is not a cell type (multiple metacells may belong to the same "type", or even have the "same" state, if the data sufficiently over-samples this state). Also, a metacell is not a parametric model of the cell state. It is merely a more robust description of some cell state.

The metacells should therefore be further analyzed as if they were cells, using additional methods to classify cell types, detect cell trajectories and/or lineage, build parametric models for cell behavior, etc. Using metacells as input for such analysis techniques should benefit both from the more robust, less noisy input; and also from the (~100-fold) reduction in the number of cells to analyze when dealing with large data (e.g. analyzing millions of individual cells).



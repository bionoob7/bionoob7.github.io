目前单细胞转录组领域用的比较多的细胞聚类方法大多是直接从基因表达矩阵推断，但是对于多样本合并分析，很多情况下会出现难以解决的批次效应，例如：

>  有些癌症多样本的聚类结果大多每个样本单独分成一群
对于发育样本，发育前期和后期细胞类型可能存在较大差异，某些样本特异的细胞群，难以判断是批次效应产生的还是真正的生物学效应。

2017年发表在Nature Methods杂志上的SCENIC[1]算法，利用单细胞RNA-seq数据，同时进行基因调控网络重建和细胞状态鉴定，应用于肿瘤和小鼠大脑单细胞图谱数据，提出并证明了**顺式调控网络分析能够用于指导转录因子和细胞状态的鉴定**。SCENIC通过使用生物学驱动的features自动清除肿瘤样本特异性等批次效应。

## 基因调控网络

我们要深刻了解SCENIC并应用在我们的项目上就要对`基因调控网络(GRN, gene regulatory network)`的背景有一些认识。细胞的转录状态来自潜在的基因调控网络，GRN由数量有限的转录因子（TFs）和辅因子相互调节及调节下游靶基因构成。

单细胞转录组分析的最新进展为高分辨率识别转录状态和状态之间的过渡提供了令人兴奋的机会。例如，在分化过程中针对单细胞RNA-seq进行优化的统计和生物信息学方法带来了新的生物学见解。但是，`稳定细胞状态下的特异性和鲁棒的（robust）GRN是否能够被确定还尚不清楚`。

鉴于在单细胞水平来做这件事情，这可能确实具有挑战性。由于转录爆发和其他来源的基因表达的随机变化，基因表达可能会与TF输入的动力学部分断开（不相关）。目前已经有一些从单细胞RNA-seq数据推断共表达网络的方法发表，但是，这些方法未使用调控序列（regulatory sequence）分析来预测TF与靶基因之间的相互作用。

作者认为，将顺式调控序列与单细胞基因表达连接可以克服缺失（基因表达丰度检测不到）和技术多样性，从而优化细胞状态的发现和表征。鉴于此，作者开发了单细胞调控网络推断和聚类（SCENIC）来绘制GRN，通过评估每个细胞中GRN的活性，来识别稳定的细胞状态。

## SCENIC workflow

![https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfrnn5hF0X0ZficicCnHI0Euia5d1k7YxAvLIzOR4zGfBHYAmneNnWBA6giag/640?wx_fmt=png](https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfrnn5hF0X0ZficicCnHI0Euia5d1k7YxAvLIzOR4zGfBHYAmneNnWBA6giag/640?wx_fmt=png)

SCENIC workflow 包含3个主要步骤：

1.用GENIE3（随机森林) 或GRNBoost (Gradient Boosting) 推断转录因子与候选靶基因之间的共表达模块。每个模块包含一个转录因子及其靶基因，纯粹基于共表达。2.使用RcisTarget分析每个共表达模块中的基因，以鉴定enriched motifs；仅保留TF motif富集的模块和targets，每个TF及其潜在的直接targets gene被称作一个`调节子（regulon）`3.使用AUCell评估每个细胞中每个regulon的活性，AUCell分数用于生成Regulon活性矩阵，通过为每个regulon设置AUC阈值，可以将该矩阵进行二值化（0|1，on|off），这将确定Regulon在哪些细胞中处于“打开”状态。

**使用RcisTarget是SCENIC不同于大多共表达算法的重要区别**。由于GENIE3模块仅基于共表达，因此结果可能包含许多误报和间接target，为了鉴定推断的直接结合的靶标基因，使用RcisTarget对每个共表达模块进行顺式调控基序（motif）分析。仅保留具有正确基因上游调节子且显着富集TF motif的模块，并对它们进行修剪以除去缺乏基序支持的间接靶标，这些处理后的模块才称为regulon。

### AUCell对regulon活性打分

![https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfr0lDpdIF5JsDuZbO5EmdCCicibQFZQN6qPTVDJ2OLAP7LqWd1mc3jND9g/640?wx_fmt=png](https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfr0lDpdIF5JsDuZbO5EmdCCicibQFZQN6qPTVDJ2OLAP7LqWd1mc3jND9g/640?wx_fmt=png)

作为SCENIC的一部分，作者开发了AUCell算法来对每个细胞中的每个regulon的活性进行评分。

对于一个给定的regulon，通过比较所有细胞间的AUCell打分值，我们可以识别哪些细胞具有更显著高的regulon活性。通过卡阈值得到的二元活性矩阵使矩阵维数减少（可理解为只有 0|1，on|off），对于下游分析很有用。例如，基于regulon二元活性矩阵的聚类，可以根据某个调控子网络（regulon）的活性来识别细胞群类型和细胞状态。由于regulon是整体评分的，而不是使用单个基因的表达，因此这种方法对于个别基因的dropouts很有效。

![https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfrAaQ9m22KcgibY8NWNqtjXia9zbibmhSnY7icpxjGuNB3EywByqRq61TbAA/640?wx_fmt=png](https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfrAaQ9m22KcgibY8NWNqtjXia9zbibmhSnY7icpxjGuNB3EywByqRq61TbAA/640?wx_fmt=png)

## SCENIC性能评估

作者对SCENIC进行了`准确性`和`鲁棒性`评估。

![https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfrc1Y3eGrlOjpYsYkTicr6skVibQMu67CLpOngtZkTy1c9qO0JVqib8kpqQ/640?wx_fmt=png](https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfrc1Y3eGrlOjpYsYkTicr6skVibQMu67CLpOngtZkTy1c9qO0JVqib8kpqQ/640?wx_fmt=png)

### SCENIC准确性评估

作者拿成年小鼠大脑的已知细胞类型的scRNA-seq数据集来测试SCENIC，来评估其准确性。

分析了1,046个初始共表达模块中的151个Regulons，这些Regulons包含对应TF的显着富集的motif（占初始TFs的7％）。每个细胞的Regulon活性评分确定了预期的细胞类型（`上图d，e`）以及每种细胞类型的潜在主调控因子的列表（例如下图中的小胶质细胞网络），细胞聚类的结果比一些专用的单细胞聚类方法(SC3[2])要准确。

![https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfrcgZjm1TfNKF2IQ1XYJF7F0gWVHLUI4kp5B3qao8xr6RN4evxISf3Cw/640?wx_fmt=png](https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfrcgZjm1TfNKF2IQ1XYJF7F0gWVHLUI4kp5B3qao8xr6RN4evxISf3Cw/640?wx_fmt=png)

### SCENIC鲁棒性评估

作者用以下方式分析了小鼠大脑单细胞RNA-seq图谱数据：

1.全部的细胞2.随机选取其中100个细胞3.三分之一的测序reads来模拟低深度的数据

SCENIC鉴定出了仅由少量细胞代表的细胞类型（例如，来自小胶质细胞，星形胶质细胞或中间神经元的2至6个细胞；下图）。此外，预测的TFs与细胞类型的关联也与先前已知的一致，并且此准确性优于标准分析流程。

![https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfrImPRG4awR7c87XicibrwzMEcVVlHnHOHib5gXouiczPSDaUj7oOnR7v8rw/640?wx_fmt=png](https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfrImPRG4awR7c87XicibrwzMEcVVlHnHOHib5gXouiczPSDaUj7oOnR7v8rw/640?wx_fmt=png)

为了验证鉴定小鼠interneurons的Dlx1/2调控网络，作者分析了人脑的sNuc-Seq(Single nuclei RNA-Seq)数据集。

![https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfrA2O65qkmpyj4LWjvJZMaPoXrOWZUGPl9l7LPUcCSIppXEaGbicPobAg/640?wx_fmt=png](https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfrA2O65qkmpyj4LWjvJZMaPoXrOWZUGPl9l7LPUcCSIppXEaGbicPobAg/640?wx_fmt=png)

在人类脑sNuc-Seq数据集上，SCENIC也鉴定出由DLX1/2强烈驱动的interneurons细胞群，该群具有与小鼠相同的motif，并且识别出一组保守的靶标，包括DLX1本身。

![https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfr0UkMz9JcicEX3FDNcDTmASnVS5WAycmmry4xZcSywBIh91sa8w2sbjw/640?wx_fmt=png](https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfr0UkMz9JcicEX3FDNcDTmASnVS5WAycmmry4xZcSywBIh91sa8w2sbjw/640?wx_fmt=png)

![https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfrWOFlrhTrfZPOLMDJLTicpnibIibF6huHwzpuPRmIRS7z0bCB9ice3PQOMQ/640?wx_fmt=png](https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfrWOFlrhTrfZPOLMDJLTicpnibIibF6huHwzpuPRmIRS7z0bCB9ice3PQOMQ/640?wx_fmt=png)

接下来，作者将这种跨物种分析扩展到其他细胞类型。与基于归一化表达的标准聚类（产生强大的物种驱动聚类）不同，SCENIC分析有效地按细胞类型对细胞进行了分组（下图）。这表明网络活性的评分是可靠的（robust），可以用来克服批处理或测序方法等技术影响。

![https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfrWmZPiaW3lKdL60YDw2IC8YfwOavxiaFucb839DAzad6X1CxVlZk8MSSQ/640?wx_fmt=png](https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfrWmZPiaW3lKdL60YDw2IC8YfwOavxiaFucb839DAzad6X1CxVlZk8MSSQ/640?wx_fmt=png)

`上图为：人和小鼠大脑scRNA-seq数据基于GRN活性的联合聚类，彩色标注的TF为人和小鼠中共同鉴定到的regulons`

### 鉴定肿瘤scRNA-seq数据集中的复杂细胞状态

由于肿瘤特异性突变和复杂的基因组畸变，癌细胞状态的鉴定比正常细胞状态更具挑战性。一般的标准聚类会通过表达矩阵将细胞按其肿瘤起源分组（各个样品聚成一类），但SCENIC的结果揭示了不同的图景。




![https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfrz3E0ybXUlpOd2K1T1hW6iahdAtbJoXvdC2OgJbyicSl3iazf8eDVPibu7Q/640?wx_fmt=png](https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfrz3E0ybXUlpOd2K1T1hW6iahdAtbJoXvdC2OgJbyicSl3iazf8eDVPibu7Q/640?wx_fmt=png)

以下是作者将SCENIC用在`少突胶质细胞瘤`（来自**6个肿瘤的4,043个细胞**）和`黑素瘤`（来自**14个样本的1,252个细胞**）的scRNA-seq数据集上的结果。

![https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfrpkVYxjRefEZ6MkC1vlaO6lhlfTzQx01HMY6s6j5OXGvmN5hlVBEdEA/640?wx_fmt=png](https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfrpkVYxjRefEZ6MkC1vlaO6lhlfTzQx01HMY6s6j5OXGvmN5hlVBEdEA/640?wx_fmt=png)

### 少突胶质细胞瘤

对于少突胶质细胞瘤（oligodendroglioma），在所以肿瘤细胞中鉴定出三种癌细胞状态（上图c-e），每种状态均由预期的TF驱动，包括：

1.oligodendrocyte-like stat：SOX10/4/8, OLIG1/2, 和 ASCL12.astrocyte-like state：ASCL1， SOX9，NFIB3.cycling cells：E2F和FOXM1

此外，作者将扩散映射（Diffusion Maps）应用于二元SCENIC矩阵（上上图）重建了从stem-like到oligodendrocyte-like 和 astrocyte-like分支的分化轨迹。值得注意的是，与正常oligodendrocyte分化相比，此路径代表不同的“轨迹”。

### 黑素瘤

在黑素瘤（melanoma）数据上观察到了类似的肿瘤效应校正，其中SCENIC识别了跨肿瘤的细胞群。包括一群与少突胶质细胞瘤中类似的TF驱动的周期细胞（例如，E2F1/2/8 和 MYBL2）

> Combat和Limma之类的专用批处理效应去除方法，需要预先指定批处理效应的来源；与这些方法相反，SCENIC通过使用生物学驱动的features自动清除肿瘤的效应。

黑色素瘤细胞大致分为两组：

1.MITFhigh state：典型的扩散状态，以MITF和STAT / IRF为主要regulators；2.MITFlow state：WNT5A, LOXL2 和 ZEB1等已知的侵袭状态的markers表达上调

SCENIC在MITFlow state的细胞群下鉴定到了两个新的TFs：

1.NFATC2 (114 predicted target genes)2.NFIB (15 predicted target genes)

NFATC2是JNK/MAPK途径中的转录阻遏物，参与黑色素瘤去分化和免疫逃逸；

NFIB与毛囊和黑素细胞干细胞的干细胞行为有关，它在小细胞肺癌转移过程中起着重要作用。

为了进一步探索NFATC2和NFIB在MITFlow状态下的潜在作用，作者对25个具有不同肿瘤进展的黑色素瘤标本进行了免疫组织化学分析。

作者发现NFIB和NFATC2在前哨淋巴结（sentinel lymph nodes）中表达最高，这与ZEB1表达共定位，这与ZEB1表达共定位，这表明这些markers的表达与最早的转移事件之间存在关系。

![https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfrmXicxiaA46ARCvX6nibsNia8EwlAlOmDpUsTl0uon5x7teVZ52a1hibMVag/640?wx_fmt=png](https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfrmXicxiaA46ARCvX6nibsNia8EwlAlOmDpUsTl0uon5x7teVZ52a1hibMVag/640?wx_fmt=png)

在A375黑色素瘤细胞系，NFATC2和NFIB的表达很高，当使用siRNA敲除NFATC2时，作者发现NFATC2调节子中的基因被显着上调。这与先前证实的NFATC2作为阻遏物是一致的。此外参与细胞粘附、细胞外基质和几个先前发表的代表黑色素瘤浸润状态调控的基因也被上调。这表明NFATC2确实可能在疾病进展中起重要作用。作为黑色素瘤regulons的第二次验证，作者使用ChIP-seq数据确定了MITF和STAT的预测靶标。

![https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfrfAzN8YvicEL5DsFd6xpnl53Rg6lKwvbiaPCiczPvib6lptznMwvyJicDlGg/640?wx_fmt=png](https://mmbiz.qpic.cn/mmbiz_png/SiacuSjV1nDxkL300vUSRmYlB22GicEGfrfAzN8YvicEL5DsFd6xpnl53Rg6lKwvbiaPCiczPvib6lptznMwvyJicDlGg/640?wx_fmt=png)

## SCENIC使用建议

随着单细胞数据集大小的增加，作者建议两种补充方法来扩展网络推断：

1.取采样数据集的子集来推断出GRN，并在AUCell评分步骤中包括所有细胞2.使用更高效的机器学习和大数据处理解决方案，作者应用GRNBoost 梯度增强来代替random-forest regression，这种实现方式大大减少了推断GRN所需的时间，并将为在非常大的数据集上进行网络推断铺平道路。

SCENIC是一种普遍适用的分析scRNA-seq数据的方法，利用TF和顺式调控序列来指导细胞状态的发现。文章的结果表明，GRNs是确定细胞状态的可靠方法，并且scRNA-seq数据非常适合跟踪基因调控过程，在基因调控过程中特定组合的TFs驱动细胞特异性的转录组。

最近发表的文章单细胞转录组测序绘制小鼠内皮细胞精细图谱[3]分析研究了转录因子在不同ECs中的调控作用，发现转录因子调控网络呈现了组织特异性。

pyscenic的使用教程请参考：https://pyscenic.readthedocs.io/en/latest/ 或者查看 pyscenic github[4]

## References

`[1]` SCENIC: *https://www.nature.com/articles/nmeth.4463*
`[2]` SC3: *https://www.nature.com/articles/nmeth.4236/*
`[3]` 单细胞转录组测序绘制小鼠内皮细胞精细图谱: *https://www.ncbi.nlm.nih.gov/pubmed/32059779*
`[4]` pyscenic github: *https://github.com/aertslab/pySCENIC*

*`[*]` http://scenic.aertslab.org
`[*]` Aibar S, González-Blas C B, Moerman T, et al. SCENIC: single-cell regulatory network inference and clustering[J]. Nature methods, 2017, 14(11): 1083-1086.*
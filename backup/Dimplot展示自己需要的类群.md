**下图中间红色圈出来的中性粒细胞混入了其他细胞类型，如何美化一下？**

![Image](https://github.com/user-attachments/assets/6e210d09-80f5-4f4d-84bf-e8ee79365c4b)

## 代码实操

```r
library(Seurat)
dir.create("~/gzh/20240530_umap_beautify")
setwd('~/gzh/20240530_umap_beautify')
load("~/gzh/pbmc3k_final_v4.rds")
load("~/silicosis/silicosis_cluster_merge.rds")
pbmc=All.merge#pbmc$group=ifelse(grepl(pattern = "NS",pbmc$stim),"ns","treat")DimPlot(pbmc,label = TRUE,split.by = "group")DimPlot(pbmc)

DimPlot(pbmc,pt.size = 0.8)
DimPlot(pbmc,shuffle =  TRUE,order = 'Neutrophil',pt.size = 0.8)
```

**结果如下，我们可以看到圆圈里的点显得很均一，看上去没有其他"混杂"的点了：**

![Image](https://github.com/user-attachments/assets/a102a8e4-04d4-44e1-be42-1693d9c0ea63)

最主要就是这三个参数，你可以调调看：

- shuffle = TRUE
- order = 'Neutrophil'
- pt.size = 0.8

## 我的感受

**以前我对数据可视化的美观度有着极大的热情，总是追求那些看起来赏心悦目的图表。然而，回过头来看，这种美观真的那么必要吗？毕竟，数据的本质并没有因为图形的展示方式而发生改变。我们只是改变了它的表现形式而已。这种精巧的技巧不应该成为痴迷的对象，了解其存在和基本应用就足够了。**
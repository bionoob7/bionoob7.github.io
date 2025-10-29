# 【单细胞高级绘图】07.KEGG富集结果展示



![](https://cubox.pro/c/filters:no_upscale()?imageUrl=https%3A%2F%2Fmmbiz.qpic.cn%2Fmmbiz_png%2FWThoCmvVu2aIBEpoXSBbHqaDd55qUPf8YtkRInq6dEbQ7lteYtD5V3pFlnW18gFPYlNC3HNsUxibRDk71HaNFGA%2F640%3Fwx_fmt%3Dpng%23imgIndex%3D0)
这一节画的图是比较新的，图中我用红色箭头标出的是pathway`一级`注释信息(big annotation，自己想的，非专有名词)，纵轴花花绿绿的标注是pathway的`二级`注释(small annotation)。**「如何获取注释」** 算一个难点，我`上一讲`也已经讲过：KEGG通路的从属/注释信息如何获取。

整个图反映的是有`多少基因`落到了对应的分类里面。

**「辩证地看」** ，整张图都是pathway注释，没有具体的pathway名称，跟平常做的富集分析很`不一样`。把图里面的二级注释换成具体的pathway会更好。另外，这个图中横坐标是`基因数`，但我觉得在富集分析中这个数值并不重要（基因ratio比单个数值重要），我们可以换成`p值`。

*** ** * ** ***

在开始画图之前，需要整理一个表格，表格中至少包含：**「pathway ID、pathway description、pathway注释/big annotation、几个富集指标、被富集到的基因」**。

*
  **「前三个信息」**，上一讲的表格已经整理好了，可以拿来用；
*
  **「几个富集指标」** 在`clusterprofiler`的输出结果中也有；
*
  **「被富集到的基因」**貌似clusterprofiler做kegg富集不能直接显示基因symbol，所以返回的结果需要稍微加工一下。

*** ** * ** ***

整理表格的代码名称为`run.R`，如下所示：
```r
    library(Seurat)
    library(tidyverse)
    library(xlsx)

    testseu=readRDS("testseu.rds")
    Idents(testseu)="anno_new"

    ### 找差异基因 #########################################################################
    marker_celltype=FindAllMarkers(testseu,logfc.threshold = 0.8,only.pos = T)
    # 过滤
    marker_celltype=marker_celltype%\>%filter(p_val_adj \< 0.01)
    marker_celltype$d=marker_celltype$pct.1-marker_celltype$pct.2
    marker_celltype=marker_celltype%\>%filter(d \> 0.2)
    marker_celltype=marker_celltype%\>%arrange(cluster,desc(avg_log2FC))
    marker_celltype=as.data.frame(marker_celltype)
    write.xlsx(marker_celltype,file = "markers_log2fc0.8_padj0.01_pctd0.2.xlsx",row.names = F,col.names = T)

    ### 富集分析 ###########################################################################
    library(clusterProfiler)
    library(org.Hs.eg.db)
    R.utils::setOption("clusterProfiler.download.method","auto") #https://github.com/YuLab-SMU/clusterProfiler/issues/256

    source("syEnrich.R")
    syEnrich(marker_celltype,outpath = "markers_log2fc0.8_padj0.01_pctd0.2")

    ### 挑一类细胞来作为演示 #######################################################
    kegg.res=read.xlsx("markers_log2fc0.8_padj0.01_pctd0.2.KEGG.xls",sheetIndex = 1,as.data.frame = T,header = T)
    kegg.res=kegg.res%\>%filter(p.adjust \< 0.05)
    kegg.res=kegg.res%\>%filter(cluster == "Endothelial")

    # 导入上一讲的文件
    kegg_info=read.xlsx("kegg_info.xlsx",sheetIndex = 1,startRow = 3)
    kegg_info=kegg_info\[,c("ID","Pathway","big.annotion")\]

    # 合并两个表格
    kegg.res$ID=str_replace(kegg.res$ID,"hsa","")
    kegg.res=kegg.res%\>%inner_join(kegg_info,by = "ID")
    write.table(kegg.res,file = "kegg.res.txt",quote = F,sep = "\t",row.names = F,col.names = T)
```
我以`单细胞分析`中的kegg富集分析作为演示，只取其中一个cluster的富集结果来画图。

上述代码中间用到的富集代码叫`syEnrich.R`，这个文件只需要输入单细胞seurat对象运行FindAllMarkers得到的差异基因，就可以返回GO/KEGG富集结果，同时被富集到某个通路的基因symbol也会被列出。

运行`run.R`之后，最终的表格如下图所示：![](https://cubox.pro/c/filters:no_upscale()?imageUrl=https%3A%2F%2Fmmbiz.qpic.cn%2Fmmbiz_png%2FWThoCmvVu2aIBEpoXSBbHqaDd55qUPf8rGduiblhibyduCaExGSVu88FXRb4XTG99cN4xS5sjykGdkic0nqA4otrQ%2F640%3Fwx_fmt%3Dpng%23imgIndex%3D1)

*** ** * ** ***

然后开始画图，代码名称为`3.plot.R`，这里就不演示了，最终可以得到的图如下：
![](https://cubox.pro/c/filters:no_upscale()?imageUrl=https%3A%2F%2Fmmbiz.qpic.cn%2Fmmbiz_png%2FWThoCmvVu2aIBEpoXSBbHqaDd55qUPf8b9eiaia4gxcpsk9HJlhZhwMBXX6LvmQUibQN2jpz3vLr6UWicK1Sibp0a0A%2F640%3Fwx_fmt%3Dpng%23imgIndex%3D2)

# 背景
实际上，无论是基因组层面的WGS、WES或GWAS，还是转录水平的芯片、二代测序和单细胞测序，以及蛋白组、代谢组等各种组学，检测流程固然重要，但数据挖掘似乎更重要。常规生信分析内容包括差异分析、**WGCNA**、**聚类分析**、降维分析、PCA、拟时序分析、互作分析和富集分析等，也包括结合各种表型的相关分析，如免疫分析、干性分析、突变分析等。**差异分析最基础，而聚类分析和WGCNA可以在差异分析结果不理想时发挥巨大作用**。

#  两条腿走路

## 1.预操作

肿瘤 ：可以先到GEPIA数据库看看，xx基因在肿瘤中的表达是否存在差异
非肿瘤：可以先到GEO2R/eVITTA中看看

>[eVITTA at UBC](https://tau.cmmt.ubc.ca/eVITTA/)

## 2.多维度

![Pasted image 20230512103824](https://github.com/user-attachments/assets/a4f9ec82-7e41-401d-bf93-f7a65817b6d2)


R + 在线网站
GEO + TCGA

## 3.差异分析，聚类分析

![Pasted image 20230512104101](https://github.com/user-attachments/assets/1b9d9550-67f5-43dc-b2e4-83f97309eab1)


- 等级聚类

- 非等级聚类
K-means聚类(Clustering)
指将数据对象分组成为多个类或者簇，使得<font color="#d83931">簇内的对象应该非常靠近，而簇会相距很远</font>。

	相关概念：分类，聚类，降维，PCA

## 4.WGCNA
![[Pasted image 20230512105029.png]]

### 1.共表达网络
颜色越深，gene之间相关性越强

### 2.模块鉴定
p < 0.05 & 相关系数较高 的模块

### 3.将模块与外部的表征或表型关联
分析相关性，找到感兴趣的模块

### 4&5 得到基因


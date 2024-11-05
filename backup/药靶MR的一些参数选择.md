## 最优参数如何设置

> Miaoran Zhang, Zhihao Xie, Aowen Tian et al. Optimizing Mendelian Randomization for Drug Prediction: Exploring Validity and Research Strategies, 28 February 2024, PREPRINT (Version 1) available at Research Square [https://doi.org/10.21203/rs.3.rs-3966011/v1]

**最优clump r2 是0.3， kb 选择 500kb即可**

这样做保证工具变量的统计效力是够的。因为我们做药物靶点MR，不像是两个疾病或者两种其它表型之间去判断因果关系，这样的数据集本身很丰富，样本量一般都很大，所以得到的IV对暴露的解释度（R2）就足够强，最大的R2比如像升高，能到40%多，接近50%。这个时候我们选择更严格的clump阈值能够避免过拟合的效果。

但是针对eqtl pqtl这一类的工具变量，他们的来源也就是几百到一千的样本量，计算出来的effect size效力就不太够。这个时候如果还用非常严格的clump阈值，会大量丢掉IV，导致最终对暴露的解释度就不够大。得到的结果F统计量也不足。

从基因组层面来说，r2 0.2， 0.3 都可以认为两个IV已经不具有连锁不平衡了，所以是可以接受的。

同时这篇文章还用真实药物靶点及适应症的数据来验证了这个观点，确实这个阈值比其它阈值能够找到的真阳性药物靶点的比例要高。

![image](https://github.com/user-attachments/assets/9f7989b0-d943-46f3-b079-2f72fd0a584b)

## 用什么组织

> Miaoran Zhang, Zhihao Xie, Aowen Tian et al. Optimizing Mendelian Randomization for Drug Prediction: Exploring Validity and Research Strategies, 28 February 2024, PREPRINT (Version 1) available at Research Square [https://doi.org/10.21203/rs.3.rs-3966011/v1]

做药靶MR 用组织特异性的eqtl 或者pqtl 作为工具变量会更好。

这个确实也很好理解，首先就目前我们做药靶的数据来说，主体分两类，一类是eqtl, 一类是pqtl。eqtl数据主要使用的是eqtlGene database和GTEx。eqtlGene 的样本都采的全血。但是GTEx是有56个组织的。pqtl的话大部分是全血的，只有一个队列是做脑的，但是样本量才不到100。总体来看，来自全血的eqtl, pqtl，基于的样本量是最大的，其次是GTEx里不同组织的eqtl，一般都有三百多个样本了。最次是脑组织的pqtl。所以按照样本量大小的排序，最好的是全血eqtl , pqtl。

作者又比较了组织特异性的eqtl对该组织的疾病，以及全血eqlt对所有组织中的疾病，发现组织特异性的eqtl作为IV 得到的真阳性率也更好。


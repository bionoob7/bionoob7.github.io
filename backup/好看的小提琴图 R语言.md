```r
set.seed(123) 
# 创建三个不同条件下的数据
ctrl_data <- data.frame(
  condition = "CTRL",
  FLI1 = rnorm(100, mean = 0.4, sd = 0.15)
)
treatment1_data <- data.frame(
  condition = "Treatment1",
  FLI1 = rnorm(100, mean = 0.6, sd = 0.2)
)
treatment2_data <- data.frame(
  condition = "Treatment2",
  FLI1 = rnorm(100, mean = 0.75, sd = 0.25)
)
# 合并数据
plot_data <- rbind(ctrl_data, treatment1_data, treatment2_data)
# 确保条件是因子并设置水平顺序
plot_data$condition <- factor(plot_data$condition, 
                              levels = c("CTRL", "Treatment1", "Treatment2"))
# 查看数据结构
head(plot_data)
```
开始画图
```r
#install.packages("ggcompare")
#install.packages("paletteer")
library(paletteer)
library(ggcompare)
library(ggplot2)
# 配色来自 "ggthemes::Tableau_10"
ggplot(plot_data, aes(x = condition, y = FLI1,fill = condition,color = condition)) +
  geom_violin(aes(), trim = FALSE, alpha = 0.6,linewidth = 0.6) +
  geom_boxplot(aes(),width = 0.3,alpha = 0.4,linewidth = 0.6) +
  scale_fill_paletteer_d("ggthemes::Tableau_10") +
  scale_color_paletteer_d("ggthemes::Tableau_10") +
  labs(x = "", y = "Expression") +
  stat_compare(ref_group = "CTRL",,breaks = c(0, 0.001, 0.01, 0.05, 1)) +
  scCustomize::theme_ggprism_mod() +
  theme(panel.grid.major.y = element_line(color = "gray80", linewidth = 0.5))

ggsave(paste0("violin_",Sys.Date(),".pdf"),width = 6,height = 4)
```

![Image](https://github.com/user-attachments/assets/5430585a-1935-4fb6-bd02-0180cb2293c5)
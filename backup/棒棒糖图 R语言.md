```r
# 生成包含基因名称和表达量的数据框
gene_data <- data.frame(
  Gene = c("TP53", "BRCA1", "EGFR", "MYC", "CDKN2A", 
           "PTEN", "KRAS", "ALK", "BRAF", "ERBB2"),
  Expression = round(runif(10, 1, 15), 1)  # 生成1-15之间的随机数，保留1位小数
)
```

```r
noob_lollipop_plot <- function(data, name_col, value_col, colors = NULL) {
  # 加载所需的包
  library(ggplot2)
  library(RColorBrewer)
  
  # 检查数据中是否包含指定的列
  if (!name_col %in% names(data) || !value_col %in% names(data)) {
    stop("Ensure that name_col and value_col exist in the data.")
  }
  
  # 设置因子顺序为数据中的顺序
  data[[name_col]] <- factor(data[[name_col]], levels = unique(data[[name_col]]))
  
  # 如果没有提供颜色向量，则生成默认颜色
  if (is.null(colors)) {
    unique_levels <- length(levels(data[[name_col]]))
    if (unique_levels <= 12) {
      # 使用 Paired 调色板
      colors <- brewer.pal(unique_levels, "Paired")
    } else {
      # 组合使用 Paired 和其他颜色
      colors <- c(brewer.pal(12, "Paired"), rainbow(unique_levels - 12))
    }
    # 给颜色向量命名，分别对应每个因子水平
    names(colors) <- levels(data[[name_col]])
  }
  
  # 创建棒棒糖图
  p <- ggplot(data, aes(x = .data[[value_col]], y = .data[[name_col]])) +  
    # 添加彩色点
    geom_point(aes(color = .data[[name_col]]), size = 10) +
    # 周围带有边框的点，使其看起来像同心圆
    geom_point(aes(color = .data[[name_col]]), size = 12, shape = 21, fill = NA) +
    # 添加线段，连接起始点和彩色点，加粗线条
    geom_segment(aes(x = 0, xend = .data[[value_col]], y = .data[[name_col]], 
                     yend = .data[[name_col]], color = .data[[name_col]]), 
                 linewidth = 2.5) +
    # 手动设置颜色
    scale_color_manual(values = colors) +
    # 使用经典主题
    theme_classic() +
    # 自定义主题设置
    theme(
      legend.position = "none",  # 不显示图例
      panel.border = element_blank(),  # 去掉面板边框
      axis.line = element_line(color = "black"),  # 保留且设置坐标轴线颜色为黑色
      panel.grid.major = element_blank(),  # 去掉主要网格线
      panel.grid.minor = element_blank(),  # 去掉次要网格线
      plot.margin = margin(1, 1, 1, 1, "cm"),  # 设置画布边距
      axis.title.x = element_text(size = 16, face = "bold"),  # 设置X轴标题字体大小和字体加粗
      axis.text.x = element_text(color = "black",size = 16),  # 设置X轴标签字体大小
      axis.text.y = element_text(color = "black",size = 16),  # 设置Y轴标签字体大小
      axis.title.y = element_text(size = 16, face = "bold")   # 设置Y轴标题字体大小和加粗
    ) +
    # 动态设置x轴的范围
    xlim(0, max(data[[value_col]]) + 0.5) +
    # 添加标签，设置X轴标题
    labs(x = value_col, y = "") +
    # 添加文本标签，显示数值并设置文本位置和大小
    geom_text(aes(label = .data[[value_col]]), hjust = -2, size = 4.5)
  
  # 显示图像
  print(p)
}

noob_lollipop_plot(gene_data, "Gene", "Expression")
```

![Image](https://github.com/user-attachments/assets/00344a79-2f3c-4266-b719-1e3956842299)
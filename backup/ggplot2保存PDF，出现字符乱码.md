Adobe Illustrator修图时，可能会出现pdf中的圆形被绘制为字体字符，通过设定useDingbats = FALSE可避免该情况。
```r
pdf("myplot.pdf", width = 4, height = 4, useDingbats = FALSE)

# 或者
ggsave("myplot.pdf", width = 4, height = 4, useDingbats = FALSE)
```
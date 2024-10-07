目前有三种常用的转录组/单细胞数据保存方法，分别为Rdata，rds和qs。

在分析数据的时候最常规的保存方法是Rdata，这种方法可以把我们想保存的信息全部糅杂在一起变成同一个文件，在下次加载的时候一并加载出来就可以了，是初学者必学的保存数据类型。

但Rdata文件不能很个性化的修改名称，这时候如果不小心出现重名文件就会把上一次的文件给覆盖掉。因此选择rds格式的保存方式可以个性化的进行保存和读取文件，不用担心覆盖的情况，并且保存和读取速度也可能会(**实际不一定!**)比Rdata会快一些。

但进入单细胞分析时代之后，数据大小动不动就超过了20GB，上述两种方式读取和保存文件的时间变得非常的长，而且一套分析下来有可能占用存储空间会超过200GB。此时使用rdata或者rds格式的保存方式就会出现十分“心累”的情况。

那么在这个大数据分析的时代下，有没有更加适合的数据保存方式呢？曾老师告诉我可以尝试使用qs的保存方式，这种方式可以在不损坏数据的前提下尽可能的压缩文件，并且同样的文件在保存和读取速度都比上述两种方式更快。

#### 简单总结这三种方式的优缺点。

#### 1. .Rdata

**优点：**

● 多对象保存：可以在一个 .Rdata 文件中保存多个 R 对象（如数据框、列表、模型等）。

● 兼容性好：这是 R 中最常用的文件格式之一，兼容性高，易于在不同 R 环境中使用。

● 加载灵活：加载 .Rdata 文件时，可以选择性地加载其中的某些对象，而不必加载所有对象。

**缺点：**

● 文件体积较大：相比其他格式，.Rdata 文件的体积可能较大，特别是当包含多个大型对象时。

● 加载速度慢：加载速度相对较慢，尤其是在保存了大量数据时。

#### 2. .rds

**优点：**

● 单对象保存：专门用于保存单个 R 对象，更适合需要保存单一数据框或模型的场景。

● 文件体积较小：通常比 .Rdata 文件更小，因为 .rds 文件只保存一个对象，并且通过压缩减少文件大小。

● 加载速度较快：由于文件体积小，加载速度通常比 .Rdata快。

● 灵活性：可以用 readRDS() 加载对象，并赋值给任何变量名称，而不受保存时的对象名称限制。

**缺点：**

● 仅保存单个对象：只能保存单个对象，不适合需要同时保存多个对象的场景。

● 不包含变量名称：加载 .rds 文件时，必须显式地指定变量名，否则无法直接使用。

#### 3. .qs

**优点：**

● 超高压缩率：qs 格式使用高效的压缩算法，生成的文件体积非常小，非常适合保存大数据集。

● 读写速度极快：相比于 .Rdata 和 .rds，qs 格式的读写速度显著更快，特别是在处理大数据时表现尤为出色。

● 支持多种压缩等级：可以根据需要调整压缩等级，以平衡文件大小和读写速度。

**缺点：**

● 兼容性问题：qs 格式相对较新，在一些环境中可能需要额外安装 qs 包，兼容性不如 .Rdata 和 .rds 普遍。

● 社区支持较少：由于qs格式较新，相关的资源和支持可能不如其他格式丰富。

接下来进行代码运行时长和存储文件大小比较，系统中存在这三个文件。

![https://mmbiz.qpic.cn/sz_mmbiz_png/0SOG4MpDAyGm4ibdTYxNSydYgWYWMM9gNz56fFBFxrKVmfiauUiayZtlicILo7OtL1kILVShiazA8aw25zvDNIVQBvQ/640?wx_fmt=png&from=appmsg](https://mmbiz.qpic.cn/sz_mmbiz_png/0SOG4MpDAyGm4ibdTYxNSydYgWYWMM9gNz56fFBFxrKVmfiauUiayZtlicILo7OtL1kILVShiazA8aw25zvDNIVQBvQ/640?wx_fmt=png&from=appmsg)

使用system.time()函数去计算代码运行时间，最后会返回三个数值，分别是user，system和elapsed。

● user：用户CPU时间，即R代码本身在用户模式下执行的时间。也就是R解释并运行R代码所花费的时间。

● system：系统CPU时间，即操作系统为代码执行分配的时间。通常是R在操作系统级别处理文件读写或其他系统调用所消耗的时间。

● elspsed：这是墙钟时间（wall clock time），也就是从开始到结束的实际时间，包括了user时间、system时间以及R进程在等待其他资源（如I/O操作）时所花费的时间。换句话说，elspsed时间是在运行代码时实际等待的时间。

#### 1、Rdata格式：

```R
system.time({
save(sce.all,sce,sceList,file = "sce_RAW.Rdata")
})

#   user  system elapsed 
# 22.915  38.786  67.229 

system.time({
load("sce_RAW.Rdata")
})
#   user  system elapsed 
# 21.609  12.622  34.228 

file_info <- file.info("sce_RAW.Rdata")
file_size <- file_info$size
format_file_size <- function(size) {
  if (size < 1024) {
    return(paste(size, "Bytes"))
  } else if (size < 1024^2) {
    return(paste(round(size / 1024, 2), "KB"))
  } else if (size < 1024^3) {
    return(paste(round(size / 1024^2, 2), "MB"))
  } else {
    return(paste(round(size / 1024^3, 2), "GB"))
  }
}

# 使用该函数格式化文件大小
readable_size <- format_file_size(file_size)
print(readable_size)
#[1] "18.86 GB"
```


保存需要花费1分多钟时间，加载需要花费半分钟左右，最后生成的文件大小为18.86GB。

#### 2、rds格式：

由于只能对单对象保存，这里仅保存最大的文件(sce)

```R
system.time({
saveRDS(sce,file = "sce_RDS.rds")
})
#   user  system elapsed 
#740.750  27.599 770.705 

system.time({
sce <- readRDS("sce_RDS.rds")
})
#   user  system elapsed 
# 92.853  10.411 103.214 

file_info <- file.info("sce_RDS.rds")
file_size <- file_info$size
format_file_size <- function(size) {
  if (size < 1024) {
    return(paste(size, "Bytes"))
  } else if (size < 1024^2) {
    return(paste(round(size / 1024, 2), "KB"))
  } else if (size < 1024^3) {
    return(paste(round(size / 1024^2, 2), "MB"))
  } else {
    return(paste(round(size / 1024^3, 2), "GB"))
  }
}

# 使用该函数格式化文件大小
readable_size <- format_file_size(file_size)
print(readable_size)
# [1] "12.71 GB"
```


实际情况还是挺慢的，保存时间竟然超过了10分钟..，存储大小变小了一些只有12.71GB(不过只保存了最大的sce)。

#### 3、qs格式：

同样只能对单对象保存，这里仅保存最大的文件(sce)

```R
# 需要先加载一下qs包
library(qs)
system.time({
qsave(sce,file = "sce_qs.qs")
})
#   user  system elapsed 
# 67.516  20.576  88.543  

system.time({
sce <- qread("sce_qs.qs")
})
#   user  system elapsed 
# 20.622  16.275  36.943 

file_info <- file.info("sce_qs.qs")
file_size <- file_info$size
format_file_size <- function(size) {
  if (size < 1024) {
    return(paste(size, "Bytes"))
  } else if (size < 1024^2) {
    return(paste(round(size / 1024, 2), "KB"))
  } else if (size < 1024^3) {
    return(paste(round(size / 1024^2, 2), "MB"))
  } else {
    return(paste(round(size / 1024^3, 2), "GB"))
  }
}

# 使用该函数格式化文件大小
readable_size <- format_file_size(file_size)
print(readable_size)
# [1] "3.58 GB"
```


保存和加载的时间跟Rdata差不多，存储大小只需要3.58GB，缩小了4.8倍，简直太棒了！

#### 总结:

如果需要**保存多个 R 对象**，并且**不在意文件大小和加载速度**，.**Rdata** 是一种**方便的选择**。

如果只需要保存一个对象，并且希望文件较小，.rds **可能是**一个不错的选择。

如果处理的是大数据集，并且对文件体积和加载速度有较高要求，.**qs 格式是一个极佳的选择**。


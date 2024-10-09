# 环境创建及软件安装（必须）

```Shell
conda create -n snakemake -y &&
conda activate snakemake &&
conda install mamba -y  &&
mamba install python=3.10 -y &&
mamba install snakemake -y &&
mamba install -y sra-tools fastqc trim-galore hisat2 subread multiqc samtools salmon fastp
```


# 文件夹（这个无所谓，Snakemake会自动创建）

```Shell
mkdir -p project/rnaseq
cd project/rnaseq

mkdir -p  data/rawdata/sra  data/rawdata/fastq data/rawdata/qc data/cleandata/trim_galore  data/cleandata/fastqc data/cleandata/multiqc
mkdir -p Mapping/Hisat2  
mkdir -p Expression/featureCounts  
tree
```


# 参考基因组（必须）

https://www.jianshu.com/p/6298877b6253

https://www.ensembl.org/info/data/ftp/index.html?redirect=no

## 参考基因组文件

```Shell
nohup wget  https://ftp.ensembl.org/pub/release-100/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna.toplevel.fa.gz &
```


## 基因组注释文件

```Shell
 wget https://ftp.ensembl.org/pub/release-100/gtf/homo_sapiens/Homo_sapiens.GRCh38.100.gtf.gz
```


# 数据下载及解压 （非必须内容，这只是前期写的一个下载转换代码）

```Shell
#!/bin/bash

# 将此脚本的所有输出重定向到日志文件
exec > download_and_convert.log 2>&1

# SRR ID数组
srr_ids=("SRR17214819" "SRR17214820") # 根据需要添加更多SRR ID

# 原始数据目录
sra_data_dir="./data/rawdata/sra"
# FASTQ文件的输出目录
fastq_data_dir="./data/rawdata/fastq"

# 检查给定SRR ID的FASTQ文件是否已存在的函数
check_fastq_files_exist() {
    local srr_id=$1
    # 查找格式为 "${srr_id}_*.fastq.gz" 的FASTQ文件
    local files=("${fastq_data_dir}/${srr_id}"_*.fastq.gz)
    
    # 检查是否找到任何文件
    if [ -e "${files[0]}" ]; then
        return 0 # 文件存在
    else
        return 1 # 文件不存在
    fi
}

# 遍历每个SRR ID
for srr_id in "${srr_ids[@]}"; do
    # 检查FASTQ文件是否已存在
    if check_fastq_files_exist "$srr_id"; then
        echo "FASTQ文件 $srr_id 已存在，跳过..."
        continue
    fi

    # SRA文件的路径
    sra_file="${sra_data_dir}/${srr_id}/${srr_id}.sra"

    # 检查SRA文件是否已存在
    if [[ -f "$sra_file" ]]; then
        echo "SRA文件 $srr_id 已存在，但FASTQ文件不存在，进行转换..."
    else
        # Prefetch
        echo "开始预取 $srr_id"
        prefetch "$srr_id" -O "$sra_data_dir" &
        
        # 等待prefetch完成
        wait
    fi

    # Fastq-dump
    echo "开始转换 $srr_id"
    fastq-dump --split-files --gzip "$sra_file" --outdir "$fastq_data_dir" &
    
    # 等待fastq-dump完成
    wait
done

echo "所有过程已完成。"
```


# Snakemake正式流程（重要！！）

运行一个样本做例子

- **input**：输入文件

- **output**：输出文件

- **log**：输出log日志

- **shell**：命令

> 此流程中，你需要指定SAMPLE的SRR号，index路径，GTF路径即可！

```Shell
SAMPLE = ["SRR17214819", "SRR17214820"]
# 参考基因组需另外下载
index = "/home/shpc_101389/database/GRCh38.100/Hisat2Index/genome"
GTF = "/home/shpc_101389/database/GRCh38.100/Homo_sapiens.GRCh38.100.gtf"

rule all:
    input:
        expand("rawdata/sra/{sample}/{sample}.sra",sample = SAMPLE),
        expand("rawdata/fastq/{sample}_1.fastq.gz",sample = SAMPLE),
        expand("rawdata/fastq/{sample}_2.fastq.gz",sample = SAMPLE),
        expand("rawdata/qc/{sample}_1_fastqc.zip",sample = SAMPLE),
        expand("rawdata/qc/{sample}_2_fastqc.zip",sample = SAMPLE),
        expand("cleandata/trim_galore/{sample}_1_val_1.fq.gz",sample = SAMPLE),
        expand("cleandata/trim_galore/{sample}_2_val_2.fq.gz",sample = SAMPLE),
        expand("Mapping/Hisat2/{sample}.Hisat_aln.sorted.bam",sample = SAMPLE),
        expand("Mapping/Hisat2/{sample}.Hisat_aln.sorted.bam.bai",sample = SAMPLE),
        expand("Mapping/Hisat2/{sample}.flagstat",sample = SAMPLE),
        expand("Expression/featureCounts/all_feature.txt")
# 下载
rule prefetch:
    output:
        "rawdata/sra/{sample}/{sample}.sra"
    shell:
        "prefetch {wildcards.sample} -O rawdata/sra/"

# 转换
rule fasterqdump:
    input:
        "rawdata/sra/{sample}/{sample}.sra"
    output:
        fastq1="rawdata/fastq/{sample}_1.fastq.gz",
        fastq2="rawdata/fastq/{sample}_2.fastq.gz"
    shell:
        """
        fasterq-dump -p -e 10 --split-3 -O rawdata/fastq {input[0]} &&
        gzip -c rawdata/fastq/{wildcards.sample}_1.fastq > {output.fastq1} &&
        gzip -c rawdata/fastq/{wildcards.sample}_2.fastq > {output.fastq2} &&
        rm -rf rawdata/fastq/{wildcards.sample}_1.fastq &&
        rm -rf rawdata/fastq/{wildcards.sample}_2.fastq
        """


# 质控
rule fastqc:
    input:
        "rawdata/fastq/{sample}_1.fastq.gz",
        "rawdata/fastq/{sample}_2.fastq.gz"
    output:
        "rawdata/qc/{sample}_1_fastqc.zip",
        "rawdata/qc/{sample}_2_fastqc.zip"
    shell:
        "fastqc -t 5 -o rawdata/qc {input[0]}>{output[0]}"
        "fastqc -t 5 -o rawdata/qc {input[1]}>{output[1]}"

# 过滤       
rule trim_galore:
    input:
        "rawdata/fastq/{sample}_1.fastq.gz",
        "rawdata/fastq/{sample}_2.fastq.gz"
    output:
        "cleandata/trim_galore/{sample}_1_val_1.fq.gz",
        "cleandata/trim_galore/{sample}_2_val_2.fq.gz"
    log:
        "cleandata/trim_galore/trim_galore_{sample}.log"
    shell:
        "trim_galore --phred33 -q 20 --length 36 --stringency 3 --fastqc --paired --max_n 3 -o cleandata/trim_galore {input[0]} {input[1]}>{log}"
        
# 比对 建索引        
rule hisat2:
    input:
        "cleandata/trim_galore/{sample}_1_val_1.fq.gz",
        "cleandata/trim_galore/{sample}_2_val_2.fq.gz"
    output:
        "Mapping/Hisat2/{sample}.Hisat_aln.sorted.bam",
        "Mapping/Hisat2/{sample}.Hisat_aln.sorted.bam.bai"
    log:
        "Mapping/Hisat2/hisat2_{sample}.log"
    shell:
        """
        hisat2 --threads 5 -x {index} -1 {input[0]} -2 {input[1]} | 
        samtools sort --threads 1 -o {output[0]} &&
        samtools index --threads 5 {output[0]} {output[1]} > {log}
        """
        
# 统计比对结果
rule samtools:
    input:
        "Mapping/Hisat2/{sample}.Hisat_aln.sorted.bam"
    output:
        "Mapping/Hisat2/{sample}.flagstat"
    shell:
        "samtools flagstat -@ 5 {input} > {output}"
 
# 定量       
rule featureCounts:
    input:
        expand("Mapping/Hisat2/{sample}.Hisat_aln.sorted.bam",sample = SAMPLE)
    output:
        "Expression/featureCounts/all_feature.txt"
    log:
        "Expression/featureCounts/featureCounts.log"
    shell:
        "featureCounts -T 8 -p -t exon -g gene_id -a {GTF} -o {output} {input} 2>{log}"  
```


# 执行命令

```Shell
nohup snakemake --cores 16 --keep-going -s snakefile >snakemake.log &
```
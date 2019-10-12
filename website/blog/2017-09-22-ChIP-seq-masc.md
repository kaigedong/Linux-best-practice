---
title: ChIP-seq-macs
author: woobamboo
authorURL:
authorFBID:
---



from MACS manual ：***Identifying ChIP-seq enrichment using MACS***

步骤：`版本1.4`

1. 安装macs
2. 安装可选软件

<!--truncate-->

```
bowtie; samtools;R;PeakSplitter;igv;
```
```
下载bowtie index;
```

3. 运行MACS to call peaks


+ **以下用不同的数据来阐述如何使用不同的参数来运行macs。**

用**A来call FoxA1 peaks**; 用**B来 call H3K4me3 peaks 并打开片段大小评估**；用**C来call H3K4me3 peaks 用一个固定的片段大小**；用**D来call H3K4me4 peaks**

---
## A: Call FoxA1 peaks

+ 解压hg19 index 文件：`unzip hg19.ebwt.zip`
+ 下载FoxA1 raw reads from http://cistrome.dfci.harvard.edu/MACSNatureProtocol/HAIB_T47D_FoxA1.tar.gz 解压并将raw reads map到参考基因组上，使用bowtie：

```shell
tar xzvf HAIB_T47D_FoxA1.tar.gz
bowtie -m 1 -S -q /path_to/hg19 HAIB_T47D_FoxA1.fastq HAIB_T47D_FoxA1.sam
```

**参数说明**
>**`-m 1`** #specifies that reads with only one hit on the genome are retained;
>
>**`-S`** #specifies the output format as SAM;
>
>**`-q`** #specifies the input format as FASTQTQ;
>
>**`/path_to/`** #is the directory containing the unzipped bowtie prebuilt indexes; and 
>
>**`HAIB_T47D_FoxA1.fastq`** #contains the downloaded raw reads for FoxA1.

+ 运行macs

```shell
macs -t HAIB_T47D_FoxA1.sam -n HAIB_T47D_FoxA1 -g hs -B -S --call-subpeaks
```

**MACS版本**
>直接使用`macs`调用的是`1.4.2`
>要使用macs 2 可使用命令：`macs2`

### 输出文件说明

| File name                          | Description                              |
| ---------------------------------- | ---------------------------------------- |
| HAIB_T47D_FoxA1_model.r            | 一个R脚本可用来生成峰的PDF                          |
| HAIB_T47D_FoxA1_model.pdf          | 执行R脚本后生成，PDF文件描述了read在峰上的分布和片段大小评估       |
| HAIB_T47D_FoxA1_peaks.xls          | MACS使用的主要参数和鉴定出来的峰的细节信息                  |
| HAIB_T47D_FoxA1_peaks.bed          | 峰的坐标BED格式                                |
| HAIB_T47D_FoxA1_peaks.subpeaks.bed | Subpeak locations in BED-like format.由PeakSplitter 生成, 被MACS调用。 |
| HAIB_T47D_FoxA1_summits.bed        | Summit locations of the peaks in BED format |
| HAIB_T47D_FoxA1_MACS_bedGraph      | Directory where the BedGraph files are generated. For each control or ChIP-seq sample, a BedGraph file describes the read distribution along the whole genome |



**参数说明**

>**`-t`**  指定ChIP-seq read比对结果的文件名。MACS支持并能自动识别： SAM, BAM, BED, ELAND, ELANDMULTI, ELANDMULTIPET, ELANDEXPORT and BOWTIE. 使用参数`--format` 可以覆盖自动识别的格式。
>
>**`-g`** 指定基因组大小。`hs` 参数是人基因组大小的近似的缩写。等于2.7e9.
>
>**`-n`** 应用` ‘HAIB_T47D_FoxA1’`作为输出文件的前缀。
>
>**`-B`**  生成bedGraph格式的文件包含拓展的每个碱基对的read 堆积的信号文件。这个步骤很耗费内存和时间。因此，只有在需要bedGraph文件时，才指定-B.
>
>**`-S`**对于真个基因组生成单一的bedGraph 文件。否则的话，会对每个染色体生成一个。 

**其他可能会遇到的参数**
>**`--bw`** sets the ‘bandwidth’, which is half of the sliding window size used in the model-building step.
>
>**`--mfold`** specifies an interval of high-confidence enrichment ratio against the background on which to build the model. The default value ‘10, 30’ means that a model will be built on the basis of regions having read counts that are 10- to 30-fold of the background.
>
>**`--pvalue`** establishes a threshold P value: only peaks surpassing the threshold will be reported. The default threshold is 10 − 5.Users can first set a loose P value cutoff so that a sufficient number of peaks will be reported and then select peaks having the smallest P values for downstream analyses.

+ 检查MACS在终端的输出状态：

```
INFO @ Sun, 03 Jun 2012 23:36:03:
# ARGUMENTS LIST:
# name  =   HAIB_T47D_FoxA1
# format  =   AUTO
# ChIP-seq file  =   HAIB_T47D_FoxA1.sam
# control file  =   None
# effective genome size  =   2.70e + 09
# band width  =   300
# model fold  =   10,30
# pvalue cutoff  =   1.00e-05
# Large dataset will be scaled towards smaller dataset.
# Range for calculating regional lambda is: 10000 bps
INFO #1 read tag files.
INFO #1 read treatment tags.
INFO Detected format is: SAM
 < Several lines are skipped > 
INFO #2 Build Peak Model.
INFO #2 number of paired peaks: 16586
INFO #2 finished!
INFO #2 predicted fragment length is 114 bps
INFO #2.2 Generate R script for model : HAIB_T47D_FoxA1_model.r
INFO #3 Call peaks.
INFO #3 shift treatment data
INFO #3 merge  + /- strand of treatment data
INFO #3 save the shifted and merged tag counts into bedGraph file.
INFO write to a bedGraph file
 < Several lines are skipped > 
INFO #3 call peak candidates
INFO #3 use self to calculate local lambda and filter peak candidates.
INFO #3 Finally, 74761 peaks are called!
INFO #4 Write output xls file. HAIB_T47D_FoxA1_peaks.xls
INFO #4 Write peak bed file. HAIB_T47D_FoxA1_peaks.bed
INFO #4 Write summits bed file. HAIB_T47D_FoxA1_summits.bed
INFO #5 Done! Check the output files!
INFO #6 Try to invoke PeakSplitter.
INFO #6 Please check HAIB_T47D_FoxA1_peaks.subpeaks.bed file for PeakSplitter 
output!
```

> 输出的信息如下：**日期（第一行）；主要参数（以`#`开头的行）；运行过程（以`INFO`开头的行）。**运行过程中的时间信息已经被移除了（为了更直观）。如果MACS遇到了异常（比如MACS评估一个片段大小太小），警告信息将会出现。

> ! 注意 尽管警告信息不影响MACS运行，仍需要认真对待警告。例如，
>
> 警告信息：**`unbalanced reads between treatment and control`**means that the FDR of the resulting peaks will be overestimated when the control sample has more reads and will be underestimated when the ChIP-seq sample is sequenced more deeply. 
>
> 警告信息：**`Fewer paired peaks X than 1,000`** means that MACS only identified X model peaks and may indicate potential data quality issues because 1,000 model peaks are needed to robustly estimate ChIP-DNA fragment size. 
>
> 警告信息**`missing chromosome X data`**might suggest that **the raw input file for that chromosome is incomplete**.

+ 生成peak模型的PDF图像（假设R已经被安装）

```R
Rscript HAIB_T47D_FoxA1_model.r
```

>在当前目录生成名为`HAIB_T47D_FoxA1_model.pdf`的文件，图像描述了reads在峰的模型中正义链反义链上的分布和评估的片段的大小。

---


## B: call H3K4me3 peaks 

+ **数据的获取**：数据包含一个`control replicate` 和一个 `ChIP-seq replicate`

```shell
nohup wget -c  http://cistrome.dfci.harvard.edu/MACSNatureProtocol/UW_K562_H3K4me3.tar.gz &
tar xvzf UW_K562_H3K4me3.tar.gz
```

**运行MACS:**

```shell
macs -t UW_K562_H3K4me3.bam -c UW_K562_H3K4me3_Control.bam -g hs -n UW_K562_H3K4me3 -B -S --call-subpeaks
```

>参数`-c`指明了control 样本的文件名。

+ **检查MACS的屏幕输出，如果运行成功，MACS会输出如下信息：
```
 < Several lines are skipped > 
INFO : #2 Build Peak Model.
INFO : #2 number of paired peaks: 12267
INFO : #2 finished!
INFO : #2 predicted fragment length is 156 bps
 < Several lines are skipped > 
INFO : #3 use control data to filter peak candidates.
INFO : #3 Finally, 20632 peaks are called!
INFO : #3 find negative peaks by swapping treat and control
INFO : #3 Finally, 4006 peaks are called!
 < Several lines are skipped > 
```

+ **生成PDF图像:**

```shell
Rscript UW_K562_H3K4me3_model.r
```

>对于输出文件：因为这一步比上一个多用了一个control sample，会生成额外的文件`UW_K562_H3K4me3_negative_peaks.xls`包含了peaks called by comparing the control sample with the ChIP-seq sample. 这些peaks 被MACS用来评估报告的ChIP-seq峰的FDR。

---

## C: call H3K4me3 peaks with a specified DNA fragment size

+ **下载数据**：包含一个ChIP-Seq replicate 和两个control replicates。**两个control要被合并**。

```shell
nohup wget -c  http://cistrome.dfci.harvard.edu/MACSNatureProtocol/BROAD_GM12878_H3K4me3.tar.gz 1>download.log 2>&1&
tar xvzf BROAD_GM12878_H3K4me3.tar.gz
samtools merge BROAD_GM12878_H3K4me3_Control.bam BROAD_GM12878_H3K4me3_Control_1.bam BROAD_GM12878_H3K4me3_Control_2.bam
```

+ **运行MACS：**

```shell
macs -t BROAD_GM12878_H3K4me3.bam -c BROAD_GM12878_H3K4me3_Control.bam -g hs -n BROAD_GM12878_H3K4me3 -B -S --call-subpeaks
```

+ 检查屏幕输出，应该包含下列行：

```
 < Several lines are skipped > 
INFO : #2 number of paired peaks: 31077
INFO : #2 finished!
INFO : #2 predicted fragment length is 53 bps
 < Several lines are skipped > 
```

>MACS 建立的模型片段长度为53，是不同寻常的短在典型的ChIP-Seq 实验中。因此，修改参数重新运行MACS：

+ 可使用Ctrl + C 终止MACS的运行。然后移除文件夹：`BROAD_GM12878_H3K4me3_MACS_bedGraph `

```shell
rm -rf BROAD_GM12878_H3K4me3_MACS_bedGraph
```

+ 重新运行MACS：

```shell
macs14 -t BROAD_GM12878_H3K4me3.bam -c BROAD_GM12878_H3K4me3_Control.bam -g hs -n BROAD_GM12878_H3K4me3 --nomodel --shiftsize 73 -B -S --call-subpeak
```

---

## D:Calling H3K36me3 peaks

+ **下载数据**:该数据包含两个control replicates 和两个ChIP-seq replicates

```shell
  http://cistrome.dfci.harvard.edu/MACSNatureProtocol/BROAD_GM12878_H3K36me3.tar.gz
  tar xvzf BROAD_GM12878_H3K36me3.tar.gz
  samtools merge BROAD_GM12878_H3K36me3.bam BROAD_GM12878_H3K36me3_1.bam BROAD_GM12878_H3K36me3_2.bam
  samtools merge BROAD_GM12878_H3K36me3_Control.bam BROAD_GM12878_H3K36me3_Control_1.bam BROAD_GM12878_H3K36me3_Control_2.bam
```

+ **Run MACS:**

```
macs -t BROAD_GM12878_H3K36me3.bam -c BROAD_GM12878_H3K36me3_Control.bam -g hs -n BROAD_GM12878_H3K36me3 --nomodel --shiftsize 73 -B -S --pvalue 1e-3 --call-subpeaks
```

>与默认`--pvalue 1e-5`相比，这一步使用的p-value`--pvalue 1e-3`不太严格。因为H3K36me3 ChIP-seq 数据经常来自有更宽的不太集中的区域，参数`--nomodel` 和 `--shiftsize 73`优先采用。

---

# 使用IGV加载MACS的结果

1. 首先解压并重命名 `bedGraph` 文件。以FoxA1数据集为例，首先定位到` HAIB_T47D_FoxA1_MACS_bedGraph/treat`,
2. 解压文件 `gzip -d HAIB_T47D_FoxA1_treat_afterfiting_all.bdg.gz`
3. 更改后缀为`bedGraph`，这样可被IGV识别` mv HAIB_T47D_FoxA1_treat_afterfiting_all.bdg HAIB_T47D_FoxA1_treat_afterfiting_all.bedGraph`
4. 用IGV加载` HAIB_T47D_FoxA1_treat_afterfiting_all.bedGraph`，根据IGV的手册。

---

## macs2的用法(部分)

https://pypi.python.org/pypi/MACS2

### Usage of MACS2

```
macs2 [-h] [--version]
{callpeak,filterdup,bdgpeakcall,bdgcmp,randsample,bdgdiff,bdgbroadcall}
```

#### Example for regular peak calling

```
macs2 callpeak -t ChIP.bam -c Control.bam -f BAM -g hs -n test -B -q 0.01
```

### Example for broad peak calling

```	
macs2 callpeak -t ChIP.bam -c Control.bam --broad -g hs --broad-cutoff 0.1
```

### 几个常用的**sub-commands**

**callpeak**:	Main MACS2 Function to Call peaks from alignment results.
**bdgpeakcall**:	Call peaks from bedGraph output.
**bdgbroadcall**:	Call broad peaks from bedGraph output.
**bdgcmp**:	Deduct noise by comparing two signal tracks in bedGraph.
**bdgdiff**:	Differential peak detection based on paired four bedgraph files.
**filterdup**eads at the same position, then convert acceptable format to BED format.
**predictd**:	Predict d or fragment size from alignment results.
**pileup**:	Pileup aligned reads with a given extension size (fragment size or d in MACS language). Note there will be no step for duplicate reads filtering or sequencing depth scaling, so you may need to do certain post- processing.
**randsample**:	Randomly sample number/percentage of total reads.
**refinepeak**:	(Experimental) Take raw reads alignment, refine peak summits and give scores measuring balance of forward- backward tags. Inspired by SPP.

**下面只讲`callpeak`模块**，其他内容可以`macs2 COMMAND -h`来查看细节

+ **Call peaks**

  是macs2的主要功能，可以用`macs2 callpeak`命令直接调用。如果不加参数直接输这个命令，你将会看到这个参数的详细描述。

+ **Options**

  + `-t/–treatment FILENAME`:这是唯一必须的参数，如果你有多个alignment files，你可以这样指明它们`-t A B C`,macs 将会pool up all these files
  + `-c/–control `：与`-t`参数相同
  + `-n/–name`:实验的名字，MACS将会使用这个名字来生成输出文件如`NAME_peaks.xls`
  + `–outdir`:MACS将通过这个参数将文件输出到指定的文件夹
  + `-f/–format FORMAT`:Format of tag file, can be “ELAND”, “BED”, “ELANDMULTI”, “ELANDEXPORT”, “ELANDMULTIPET” (for pair-end tags), “SAM”, “BAM”, “BOWTIE”, “BAMPE” or “BEDPE”. Default is “AUTO” which will allow MACS to decide the format automatically. “AUTO” is also usefule when you combine different formats of files. Note that MACS can’t detect “BAMPE” or “BEDPE” format with “AUTO”, and you have to implicitly specify the format for “BAMPE” and “BEDPE”.

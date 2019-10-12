---
title: Counting known microRNAs in five easy steps
author: woobamboo
authorURL:
authorFBID:
---



获取一个示例的数据集

获取当前miRBase 的成熟序列

将尿嘧啶转为胸腺嘧啶

通过一行命令搜索并计算：

通过一行命令搜索并计算

<!--truncate-->



# Counting known microRNAs in five easy steps

翻译自[这里](http://www.ark-genomics.org/events-online-training/counting-known-micrornas-five-easy-steps)

假设，当你来到这里，你应该是知道[microRNAs](https://en.wikipedia.org/wiki/MicroRNA)是什么. 或许有人看过我的演讲[UK Next-generation sequencing](http://www.nottingham.ac.uk/deepseq/documents/ngs2011finalagenda.pdf) ,标题为`21 ‐ 23 facts; the devil in the detail of microRNA‐Seq`.  对于这一小类的RNA, 它们肯定是有很大的影响，现在已有大量的生物信息学工具来处理microRNA 测序数据(microRNA-seq). 然而，基本的工作来数已知的microRNA 是非常的简单的，每个人都能做，只用简单的Unix 技巧（下面使用`Novoalign`这个很酷的工具）。下面的代码假设你在路径中有`Novoalign`. 这事怎么做的：

> 5个小步骤来统计human 中的microRNAs

## 1. 获取一个示例的数据集

这个数据集来自发表在*Genome Research*上的人类研究：

Morin RD, O'Connor MD, Griffith M, Kuchenbauer F, Delaney A, Prabhu AL, Zhao Y, McDonald H, Zeng T, Hirst M, Eaves CJ, Marra MA. (2008) Application of massively parallel sequencing to microRNA profiling and discovery in human embryonic stem cells. Genome Res. 18(4):610-21. [[link](http://genome.cshlp.org/content/18/4/610.abstract)]

```shell
wget ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR026/SRR026762/SRR026762.fastq.gz
```

## 2. 获取当前miRBase 的成熟序列

```shell
wget ftp://mirbase.org/pub/mirbase/CURRENT/mature.fa.gz
```

## 3. 只提取human microRNAs, 为了方便，将**尿嘧啶转为胸腺嘧啶**

```shell
zcat  mature.fa.gz | grep -A 1 "^>hsa" | grep -v "^-" | sed 's/U/T/g' > human_mature.fa
```

## 4. 创建 Novoalign inde. Novoalign 是免费的，可从<http://www.novocraft.com> 得到

```shell
novoindex human_mature.nidx human_mature.fa
```

## 5. 通过一行命令搜索并计算：

```shell
novoalign -f SRR026762.fastq.gz -F STDFQ -d human_mature.nidx -l 18 -s 1 -a -R 0 -r Random -t 0 \
                 | grep "^@" | awk '$9 != "" {print $9}' \
                 | sort | uniq -c | sort -n -r > counts.txt
```

**下面看一下结果**

```
head counts.txt
#  126476 >hsa-miR-92a-3p
#  120762 >hsa-miR-103b
#  120576 >hsa-miR-103a-3p
#   62704 >hsa-miR-25-3p
#   53155 >hsa-miR-92b-3p
#   49215 >hsa-miR-372
#   42536 >hsa-miR-378a-3p
#   37287 >hsa-miR-140-3p
#   36178 >hsa-miR-21-5p
```

> 文章中的一个结论是『最丰富的miRNA是miR-103 』.
>
> 从结果看他们漏掉了一个（我猜hsa-miR-92a-3p是2008年之后发现的）

现在我们完成了。运行平台Scientific Linux 5 using Novoalign V2.07.18. 如果你有问题,[e-mail me](mick.watson@roslin.ed.ac.uk). 请注意awk 中使用的**`$9`**可能根据你的`reads identifier` 而改变， 所以**检查一下运行Novoalign时，放到了标识符哪一列。**
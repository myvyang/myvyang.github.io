---
title: "使用 Burrows-Delta 方法检测文章作者"
date: 2025-04-18
---

学习了下 [https://github.com/antirez/hnstyle/](https://github.com/antirez/hnstyle/) 这个任务用来检测Hacker News 上虚假账户的方案.

## 基本原理

首先统计整个Hacker News上的所有文章的词频, 提取出top150的词的数据, 然后做归一化: 先算出对于每个单词, 所有用户的平均词频mean(the_word), 以及平均标准差stddev(the_word), 然后

```
z-score: z = (freq - mean) / stddev
```

对于top150的词,为每个用户序列化一个数组:

```
[z-score(word1), z-score(word2), ...]
```

最后, 通过对这个数组计算余弦相似度即可.

## 注意的细节

所有用户的标准差, 经过了贝塞尔校正（Bessel's correction）调整.

```python
    mean = word_freq_sum[word] / total_users
    # Calculate variance using the computational formula: Var(X) = E(X²) - E(X)²
    variance = (word_freq_sq_sum[word] / total_users) - (mean * mean)
    # Use Bessel's correction for sample standard deviation.
    # You may wonder why to use Bessel's correction if we have all
    # the HN users. Well, the code assumes that this is still a
    # sample of a much larger theoretical population.
    corrected_variance = variance * total_users / (total_users - 1) if total_users > 1 else variance
    stddev = max(np.sqrt(corrected_variance), 1e-6)  # Avoid division by zero
```

此外还有一些额外的小处理, 避免极端值:

1. 如果标准差太小, 就向上截断到1e-6.
2. 计算全局词频时, 只处理出现2次以上的单词. 其实不影响, 因为只看top150.
3. 为什么要取topX? 如果X太大, 那么文章涉及类似话题的作者之间的距离会更小, 这样聚类出来的并非语言风格, 而是文章内容. 通常150-500词的效果比较好.

## 余弦相似度简单解释

$$
\cos(\theta) = \frac{A \cdot B}{\|A\| \|B\|}
$$

分母相当于对向量进行了归一化, 向量长度不影响结果.

分子为向量的每个维度的数值的相乘结果. 想象一下, 归一化之后, 两个向量, 在相同的分量上如果值都比较大, 则对最终的计算结果的贡献会更大. 实际上就是统计两个单位化的向量是否在尽可能多的分量上相似.

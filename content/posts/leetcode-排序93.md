---
title: "leetcode-排序93"
date: 2026-10-05T00:35:06+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 稀疏相似度

两个(具有不同单词的)文档的交集(intersection)中元素的个数除以并集(union)中元素的个数，就是这两个文档的相似度。例如，{1, 5, 3} 和 {1, 7, 2, 3} 的相似度是 0.4，其中，交集的元素有 2 个，并集的元素有 5 个。给定一系列的长篇文档，每个文档元素各不相同，并与一个 ID 相关联。它们的相似度非常“稀疏”，也就是说任选 2 个文档，相似度都很接近 0。请设计一个算法返回每对文档的 ID 及其相似度。只需输出相似度大于 0 的组合。请忽略空文档。为简单起见，可以假定每个文档由一个含有不同整数的数组表示。

输入为一个二维数组 docs，docs[i] 表示 id 为 i 的文档。返回一个数组，其中每个元素是一个字符串，代表每对相似度大于 0 的文档，其格式为 {id1},{id2}: {similarity}，其中 id1 为两个文档中较小的 id，similarity 为相似度，精确到小数点后 4 位。以任意顺序返回数组均可。


```
use std::collections::HashMap;

impl Solution {
    pub fn compute_similarities(docs: Vec<Vec<i32>>) -> Vec<String> {
        // 倒排索引：元素值 -> 包含该元素的文档 id 列表
        let mut inverted: HashMap<i32, Vec<usize>> = HashMap::new();
        for (i, doc) in docs.iter().enumerate() {
            for &word in doc {
                inverted.entry(word).or_default().push(i);
            }
        }

        // 统计每对文档的交集大小：id1 -> id2 -> 共同元素个数（id1 < id2）
        let mut pairs: HashMap<usize, HashMap<usize, usize>> = HashMap::new();
        for ids in inverted.values() {
            for i in 0..ids.len() {
                for j in i + 1..ids.len() {
                    *pairs
                        .entry(ids[i])
                        .or_default()
                        .entry(ids[j])
                        .or_insert(0) += 1;
                }
            }
        }

        // 计算相似度并格式化输出
        let mut result = Vec::new();
        for (&id1, inner) in &pairs {
            for (&id2, &common) in inner {
                let union = docs[id1].len() + docs[id2].len() - common;
                let similarity = common as f64 / union as f64;
                // 加 1e-9 修正浮点误差，保证四舍五入到小数点后 4 位
                result.push(format!("{id1},{id2}: {:.4}", similarity + 1e-9));
            }
        }

        result
    }
}
```

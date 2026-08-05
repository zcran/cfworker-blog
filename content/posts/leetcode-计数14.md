---
title: "leetcode-计数14"
date: 2026-08-01T10:40:51+08:00
tags: ["leetcode", "计数"]
draft: false
---


##  距离相等的条形码

在一个仓库里，有一排条形码，其中第 i 个条形码为 barcodes[i]。

请你重新排列这些条形码，使其中任意两个相邻的条形码不能相等。 你可以返回任何满足该要求的答案，此题保证存在答案。


```
impl Solution {
    pub fn rearrange_barcodes(mut barcodes: Vec<i32>) -> Vec<i32> {
        use std::collections::HashMap;
        use std::collections::BinaryHeap;

        // 1. 统计每个条形码的出现频率
        let mut freq = HashMap::new();
        for &code in &barcodes {
            *freq.entry(code).or_insert(0) += 1;
        }

        // 2. 使用最大堆按频率排序（频率高的优先）
        let mut heap = BinaryHeap::new();
        for (code, count) in freq {
            heap.push((count, code));
        }

        // 3. 交替放置频率最高的两个条形码
        let mut result = Vec::with_capacity(barcodes.len());

        while let Some((count1, code1)) = heap.pop() {
            result.push(code1);

            if let Some((count2, code2)) = heap.pop() {
                result.push(code2);

                // 如果还有剩余，重新放入堆中
                if count2 > 1 {
                    heap.push((count2 - 1, code2));
                }
            }

            if count1 > 1 {
                heap.push((count1 - 1, code1));
            }
        }

        result
    }
}
```

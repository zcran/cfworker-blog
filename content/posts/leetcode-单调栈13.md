---
title: "leetcode-单调栈13"
date: 2026-06-07T10:14:04+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 最多能完成排序的块 II

给你一个整数数组 arr 。

将 arr 分割成若干 块 ，并将这些块分别进行排序。之后再连接起来，使得连接的结果和按升序排序后的原数组相同。

返回能将数组分成的最多块数？

```
impl Solution {
    /// 最多能分多少块 - O(1) 空间复杂度版本
    ///
    /// 核心思想：动态维护后缀最小值，同时单次遍历
    /// 1. 先计算每个位置的后缀最小值
    /// 2. 再遍历一次，维护前缀最大值并统计切分点
    pub fn max_chunks_to_sorted(arr: Vec<i32>) -> i32 {
        let n = arr.len();
        if n == 0 { return 0; }

        // 计算后缀最小值
        let mut suffix_min = vec![0; n];
        suffix_min[n - 1] = arr[n - 1];
        for i in (0..n - 1).rev() {
            suffix_min[i] = suffix_min[i + 1].min(arr[i]);
        }

        // 遍历统计切分点
        let mut prefix_max = arr[0];
        let mut chunks = 1;
        for i in 1..n {
            if prefix_max <= suffix_min[i] {
                chunks += 1;
                prefix_max = arr[i];
            } else {
                prefix_max = prefix_max.max(arr[i]);
            }
        }
        chunks
    }
}
```

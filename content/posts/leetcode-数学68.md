---
title: "leetcode-数学68"
date: 2026-10-07T20:39:32+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 可行数组的数目

给你一个长度为 n 的数组 original 和一个长度为 n x 2 的二维数组 bounds，其中 bounds[i] = [ui, vi]。

你需要找到长度为 n 且满足以下条件的 可能的 数组 copy 的数量：

1. 对于 1 <= i <= n - 1 ，都有 (copy[i] - copy[i - 1]) == (original[i] - original[i - 1]) 。
2. 对于 0 <= i <= n - 1 ，都有 ui <= copy[i] <= vi 。

返回满足这些条件的数组数目。


```
impl Solution {
    pub fn count_arrays(original: Vec<i32>, bounds: Vec<Vec<i32>>) -> i32 {
        // copy[i] = offset + original[i]，其中 offset = copy[0] - original[0]
        // 对每个 i，约束 bounds[i][0] <= offset + original[i] <= bounds[i][1]
        // 即 offset ∈ [bounds[i][0] - original[i], bounds[i][1] - original[i]]
        // 所有约束取交集 [lo, hi]，可行 offset 个数即为答案。
        let mut lo = i32::MIN;
        let mut hi = i32::MAX;

        for (i, b) in bounds.iter().enumerate() {
            lo = lo.max(b[0] - original[i]);
            hi = hi.min(b[1] - original[i]);
        }

        // 交集为空时返回 0
        (hi - lo + 1).max(0)
    }
}
```

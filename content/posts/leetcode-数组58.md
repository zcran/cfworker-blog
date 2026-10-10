---
title: "leetcode-数组58"
date: 2026-10-10T20:38:44+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 三段式数组 I

给你一个长度为 n 的整数数组 nums。

如果存在索引 0 < p < q < n − 1，使得数组满足以下条件，则称其为 三段式数组（trionic）：

nums[0...p] 严格 递增，
nums[p...q] 严格 递减，
nums[q...n − 1] 严格 递增。

如果 nums 是三段式数组，返回 true；否则，返回 false。


```
impl Solution {
    pub fn is_trionic(nums: Vec<i32>) -> bool {
        let n = nums.len();
        let mut i = 1;

        // 第一段：严格递增
        while i < n && nums[i - 1] < nums[i] {
            i += 1;
        }
        let p = i - 1; // 递增段结束位置

        // 第二段：严格递减
        while i < n && nums[i - 1] > nums[i] {
            i += 1;
        }
        let q = i - 1; // 递减段结束位置

        // 第三段：严格递增
        while i < n && nums[i - 1] < nums[i] {
            i += 1;
        }

        // p 非起点、q 与 p 不同、第三段延伸到末尾、且 q 不是末尾
        p != 0 && q != p && i == n && q != n - 1
    }
}
```

---
title: "leetcode-贪心118"
date: 2026-10-03T12:55:42+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## K 个元素的最大和

给你一个下标从 0 开始的整数数组 nums 和一个整数 k 。你需要执行以下操作 恰好 k 次，最大化你的得分：

1. 从 nums 中选择一个元素 m 。
2. 将选中的元素 m 从数组中删除。
3. 将新元素 m + 1 添加到数组中。
4. 你的得分增加 m 。

请你返回执行以上操作恰好 k 次后的最大得分。


```
impl Solution {
    pub fn maximize_sum(nums: Vec<i32>, k: i32) -> i32 {
        // 贪心：每次选当前最大元素 m 得分最高，操作后它变成 m+1，
        // 下一轮再选它，如此反复 k 次，得分即为首项 m、公差 1 的等差数列和。
        let m = *nums.iter().max().unwrap();
        (2 * m + k - 1) * k / 2
    }
}
```

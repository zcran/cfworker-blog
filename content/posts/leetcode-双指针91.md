---
title: "leetcode-双指针91"
date: 2026-09-05T09:53:06+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 不同的平均值数目

给你一个下标从 0 开始长度为 偶数 的整数数组 nums 。

只要 nums 不是 空数组，你就重复执行以下步骤：

· 找到 nums 中的最小值，并删除它。
· 找到 nums 中的最大值，并删除它。
· 计算删除两数的平均值。

两数 a 和 b 的 平均值 为 (a + b) / 2 。

比方说，2 和 3 的平均值是 (2 + 3) / 2 = 2.5 。

返回上述过程能得到的 不同 平均值的数目。

注意 ，如果最小值或者最大值有重复元素，可以删除任意一个。


```
use std::collections::HashSet;

impl Solution {
    pub fn distinct_averages(mut nums: Vec<i32>) -> i32 {
        nums.sort_unstable();
        let n = nums.len();

        // 排序后，最小与最大配对，其平均值只需比较两数之和
        let sums: HashSet<i32> = (0..n / 2)
            .map(|i| nums[i] + nums[n - 1 - i])
            .collect();

        sums.len() as i32
    }
}
```

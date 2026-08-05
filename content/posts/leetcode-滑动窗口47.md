---
title: "leetcode-滑动窗口47"
date: 2026-07-18T11:02:32+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 学生分数的最小差值

给你一个 下标从 0 开始 的整数数组 nums ，其中 nums[i] 表示第 i 名学生的分数。另给你一个整数 k 。

从数组中选出任意 k 名学生的分数，使这 k 个分数间 最高分 和 最低分 的 差值 达到 最小化 。

返回可能的 最小差值 。


```
impl Solution {
    pub fn minimum_difference(mut nums: Vec<i32>, k: i32) -> i32 {
        let k = k as usize;
        if k <= 1 {
            return 0;
        }

        nums.sort_unstable();

        nums.windows(k)
            .map(|window| window[k - 1] - window[0])
            .min()
            .unwrap()
    }
}
```

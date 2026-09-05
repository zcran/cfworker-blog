---
title: "leetcode-前缀和91"
date: 2026-08-25T10:02:51+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 元素和最小的山形三元组 II

给你一个下标从 0 开始的整数数组 nums 。

如果下标三元组 (i, j, k) 满足下述全部条件，则认为它是一个 山形三元组 ：

i < j < k
nums[i] < nums[j] 且 nums[k] < nums[j]

请你找出 nums 中 元素和最小 的山形三元组，并返回其 元素和 。如果不存在满足条件的三元组，返回 -1 。


```
impl Solution {
    pub fn minimum_sum(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        if n < 3 {
            return -1;
        }

        // 后缀最小值数组：suf[i] 表示从 i 到末尾的最小值
        let mut suf = vec![0; n];
        suf[n - 1] = nums[n - 1];
        for i in (2..n - 1).rev() {
            suf[i] = suf[i + 1].min(nums[i]);
        }

        let mut ans = i32::MAX;
        let mut pre = nums[0]; // 前缀最小值

        for j in 1..n - 1 {
            // 检查能否形成山形：左边有更小值，右边也有更小值
            if pre < nums[j] && suf[j + 1] < nums[j] {
                ans = ans.min(pre + nums[j] + suf[j + 1]);
            }
            pre = pre.min(nums[j]);
        }

        if ans == i32::MAX { -1 } else { ans }
    }
}
```

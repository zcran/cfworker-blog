---
title: "leetcode-贪心116"
date: 2026-10-03T12:55:41+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 执行操作后的最大 MEX

给你一个下标从 0 开始的整数数组 nums 和一个整数 value 。

在一步操作中，你可以对 nums 中的任一元素加上或减去 value 。

例如，如果 nums = [1,2,3] 且 value = 2 ，你可以选择 nums[0] 减去 value ，得到 nums = [-1,2,3] 。

数组的 MEX (minimum excluded) 是指其中数组中缺失的最小非负整数。

例如，[-1,2,3] 的 MEX 是 0 ，而 [1,0,3] 的 MEX 是 2 。

返回在执行上述操作 任意次 后，nums 的最大 MEX 。


```
impl Solution {
    pub fn find_smallest_integer(nums: Vec<i32>, value: i32) -> i32 {
        // 同余类统计：nums[i] 只能通过加减 value 变成同余类中的任意值。
        // 因此每个余数类 r 中的元素，可依次填充 r, r+value, r+2*value, ...
        let v = value as usize;
        let mut count = vec![0i32; v];
        for &x in &nums {
            // 处理负数取模，保证下标非负
            count[(x.rem_euclid(value)) as usize] += 1;
        }

        // 从 0 开始逐一尝试填充，若某个数所在余数类已无可用元素，则该数即为 MEX
        let mut mex = 0;
        while count[mex as usize % v] > 0 {
            count[mex as usize % v] -= 1;
            mex += 1;
        }
        mex
    }
}
```

---
title: "leetcode-前缀和85"
date: 2026-08-25T10:02:51+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 最大或值

给你一个下标从 0 开始长度为 n 的整数数组 nums 和一个整数 k 。每一次操作中，你可以选择一个数并将它乘 2 。

你最多可以进行 k 次操作，请你返回 nums[0] | nums[1] | ... | nums[n - 1] 的最大值。

a | b 表示两个整数 a 和 b 的 按位或 运算。


```
impl Solution {
    pub fn maximum_or(nums: Vec<i32>, k: i32) -> i64 {
        let n = nums.len();
        let k = k as i64;

        // 后缀或数组：suf[i] = nums[i] | nums[i+1] | ... | nums[n-1]
        let mut suf = vec![0i64; n + 1];
        for i in (0..n).rev() {
            suf[i] = suf[i + 1] | nums[i] as i64;
        }

        let mut ans = 0i64;
        let mut pre = 0i64;

        // 枚举每个元素作为被乘 2^k 的元素
        for i in 0..n {
            let curr = pre | ((nums[i] as i64) << k) | suf[i + 1];
            ans = ans.max(curr);
            pre |= nums[i] as i64;
        }

        ans
    }
}
```

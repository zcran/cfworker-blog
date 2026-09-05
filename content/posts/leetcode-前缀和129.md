---
title: "leetcode-前缀和129"
date: 2026-08-25T10:02:54+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 分割的最大得分

给你一个长度为 n 的整数数组 nums。

请你选出一个下标 i 以分割数组，该下标满足 0 <= i < n - 1。

对于选择的分割下标 i：

令 prefixSum(i) 表示数组前缀的和，即 nums[0] + nums[1] + ... + nums[i]。
令 suffixMin(i) 表示数组后缀的最小值，即 nums[i + 1], nums[i + 2], ..., nums[n - 1] 中的最小值。

在下标 i 的 分割得分 定义为：

score(i) = prefixSum(i) - suffixMin(i)

返回所有有效分割下标中 最大 的分割得分。


```
impl Solution {
    pub fn maximum_score(nums: Vec<i32>) -> i64 {
        let n = nums.len();
        // 后缀最小值数组，suf_min[i] 表示 nums[i..] 的最小值
        let mut suf_min = vec![0; n];
        suf_min[n - 1] = nums[n - 1] as i64;
        for i in (0..n - 1).rev() {
            suf_min[i] = suf_min[i + 1].min(nums[i] as i64);
        }

        let mut ans = i64::MIN;
        let mut pre_sum = 0i64;
        // 分割点 i 有效范围 0..n-2
        for i in 0..n - 1 {
            pre_sum += nums[i] as i64;
            let score = pre_sum - suf_min[i + 1];
            if score > ans {
                ans = score;
            }
        }
        ans
    }
}
```

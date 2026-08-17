---
title: "leetcode-模拟140"
date: 2026-08-08T11:31:17+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 分割等和子集

给定一个非空的正整数数组 nums ，请判断能否将这些数字分成元素和相等的两部分。

```
impl Solution {
    /// 判断能否将数组分成元素和相等的两部分（0/1背包）
    pub fn can_partition(nums: Vec<i32>) -> bool {
        let sum: i64 = nums.iter().map(|&x| x as i64).sum();
        if sum % 2 != 0 { return false; }

        let target = (sum / 2) as usize;
        let mut dp = vec![false; target + 1];
        dp[0] = true;

        for &num in &nums {
            let num = num as usize;
            if num > target { continue; }
            for j in (num..=target).rev() {
                dp[j] = dp[j] || dp[j - num];
            }
            if dp[target] { return true; }
        }

        dp[target]
    }
}
```

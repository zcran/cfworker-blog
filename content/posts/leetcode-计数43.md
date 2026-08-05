---
title: "leetcode-计数43"
date: 2026-08-01T10:40:53+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 统计一个数组中好对子的数目

给你一个数组 nums ，数组中只包含非负整数。定义 rev(x) 的值为将整数 x 各个数字位反转得到的结果。比方说 rev(123) = 321 ， rev(120) = 21 。我们称满足下面条件的下标对 (i, j) 是 好的 ：

0 <= i < j < nums.length
nums[i] + rev(nums[j]) == nums[j] + rev(nums[i])

请你返回好下标对的数目。由于结果可能会很大，请将结果对 10^9 + 7 取余 后返回。


```
impl Solution {
    pub fn count_nice_pairs(nums: Vec<i32>) -> i32 {
        use std::collections::HashMap;
        const MOD: i64 = 1_000_000_007;

        // 反转数字
        fn reverse(mut x: i32) -> i32 {
            let mut res = 0;
            while x > 0 {
                res = res * 10 + x % 10;
                x /= 10;
            }
            res
        }

        let mut count = HashMap::new();
        let mut ans = 0i64;

        for num in nums {
            let key = num - reverse(num);
            let prev = count.get(&key).unwrap_or(&0);
            ans = (ans + *prev as i64) % MOD;
            *count.entry(key).or_insert(0) += 1;
        }

        ans as i32
    }
}
```

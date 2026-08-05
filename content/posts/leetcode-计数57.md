---
title: "leetcode-计数57"
date: 2026-08-01T10:40:54+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 使数组变成交替数组的最少操作数

给你一个下标从 0 开始的数组 nums ，该数组由 n 个正整数组成。

如果满足下述条件，则数组 nums 是一个 交替数组 ：

nums[i - 2] == nums[i] ，其中 2 <= i <= n - 1 。
nums[i - 1] != nums[i] ，其中 1 <= i <= n - 1 。

在一步 操作 中，你可以选择下标 i 并将 nums[i] 更改 为 任一 正整数。

返回使数组变成交替数组的 最少操作数 。


```
use std::collections::HashMap;

impl Solution {
    pub fn minimum_operations(nums: Vec<i32>) -> i32 {
        let n = nums.len();

        // 统计偶数下标和奇数下标中每个数字的出现频率
        // 返回 (最大频率, 最大频率对应的值, 次大频率, 次大频率对应的值)
        fn get_top2(nums: &[i32], start: usize) -> (i32, usize, i32, usize) {
            let mut freq = HashMap::new();
            for i in (start..nums.len()).step_by(2) {
                *freq.entry(nums[i]).or_insert(0) += 1;
            }

            let mut max_val = 0;
            let mut max_cnt = 0;
            let mut second_val = 0;
            let mut second_cnt = 0;

            for (&val, &cnt) in &freq {
                if cnt > max_cnt {
                    second_val = max_val;
                    second_cnt = max_cnt;
                    max_val = val;
                    max_cnt = cnt;
                } else if cnt > second_cnt {
                    second_val = val;
                    second_cnt = cnt;
                }
            }

            (max_val, max_cnt, second_val, second_cnt)
        }

        let (e_max_val, e_max_cnt, e_second_val, e_second_cnt) = get_top2(&nums, 0);
        let (o_max_val, o_max_cnt, o_second_val, o_second_cnt) = get_top2(&nums, 1);

        // 如果两个位置出现最多的数字不同，直接保留它们
        if e_max_val != o_max_val {
            return (n - e_max_cnt - o_max_cnt) as i32;
        }

        // 否则需要选择保留偶数位最大 + 奇数位次大，或偶数位次大 + 奇数位最大
        let keep = (e_max_cnt + o_second_cnt).max(o_max_cnt + e_second_cnt);
        (n - keep) as i32
    }
}
```

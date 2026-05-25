---
title: "leetcode-位掩码15"
date: 2026-05-18T10:27:59+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 最小不兼容性

给你一个整数数组 nums​​​ 和一个整数 k 。你需要将这个数组划分到 k 个相同大小的子集中，使得同一个子集里面没有两个相同的元素。

一个子集的 不兼容性 是该子集里面最大值和最小值的差。

请你返回将数组分成 k 个子集后，各子集 不兼容性 的 和 的 最小值 ，如果无法分成分成 k 个子集，返回 -1 。

子集的定义是数组中一些数字的集合，对数字顺序没有要求。

```
use std::collections::HashMap;

impl Solution {
    pub fn minimum_incompatibility(nums: Vec<i32>, k: i32) -> i32 {
        let n = nums.len();
        let k = k as usize;
        let group = n / k;
        let full = 1 << n;

        // 预计算合法子集极差
        let mut value = vec![-1; full];
        for mask in 0..full {
            if mask.count_ones() as usize != group {
                continue;
            }
            let mut min_val = 20;
            let mut max_val = 0;
            let mut seen = 0u16;
            let mut ok = true;
            for i in 0..n {
                if (mask >> i) & 1 == 1 {
                    let x = nums[i] as usize;
                    if (seen >> x) & 1 == 1 {
                        ok = false;
                        break;
                    }
                    seen |= 1 << x;
                    min_val = min_val.min(x);
                    max_val = max_val.max(x);
                }
            }
            if ok {
                value[mask] = (max_val - min_val) as i32;
            }
        }

        let mut memo = HashMap::new();

        fn dfs(
            mask: usize,
            value: &[i32],
            group: usize,
            n: usize,
            nums: &[i32],
            memo: &mut HashMap<usize, i32>,
        ) -> i32 {
            if mask == (1 << n) - 1 {
                return 0;
            }
            if let Some(&res) = memo.get(&mask) {
                return res;
            }

            let mut remain = 0;
            let mut seen_val = 0u16;
            for i in 0..n {
                if (mask >> i) & 1 == 0 {
                    let x = nums[i] as usize;
                    if (seen_val >> x) & 1 == 0 {
                        seen_val |= 1 << x;
                        remain |= 1 << i;
                    }
                }
            }
            // 修复括号
            if (seen_val.count_ones() as usize) < group {
                memo.insert(mask, i32::MAX);
                return i32::MAX;
            }

            let mut best = i32::MAX;
            let mut sub = remain;
            while sub != 0 {
                if value[sub] != -1 {
                    let next = mask | sub;
                    let cand = dfs(next, value, group, n, nums, memo);
                    if cand != i32::MAX {
                        best = best.min(value[sub] + cand);
                    }
                }
                sub = (sub - 1) & remain;
            }
            memo.insert(mask, best);
            best
        }

        let ans = dfs(0, &value, group, n, &nums, &mut memo);
        if ans == i32::MAX { -1 } else { ans }
    }
}
```

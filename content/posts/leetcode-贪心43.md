---
title: "leetcode-贪心43"
date: 2026-10-03T12:55:36+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 划分数组为连续数字的集合

给你一个整数数组 nums 和一个正整数 k，请你判断是否可以把这个数组划分成一些由 k 个连续数字组成的集合。
如果可以，请返回 true；否则，返回 false。


```
use std::collections::HashMap;

impl Solution {
    pub fn is_possible_divide(nums: Vec<i32>, k: i32) -> bool {
        let k = k as usize;
        // 总数必须能被 k 整除，否则无法划分
        if nums.len() % k != 0 {
            return false;
        }

        // 统计每个数字的出现次数
        let mut count: HashMap<i32, i32> = HashMap::new();
        for &x in &nums {
            *count.entry(x).or_insert(0) += 1;
        }

        // 排序后，每次取当前最小且仍有剩余的数字作为一组起点，
        // 消耗其后续 k 个连续数字各一次
        let mut nums = nums;
        nums.sort_unstable();

        for x in nums {
            match count.get_mut(&x) {
                Some(&mut 0) | None => continue, // 已被用完
                Some(v) => *v -= 1,
            }
            for y in x + 1..x + k as i32 {
                match count.get_mut(&y) {
                    Some(v) if *v > 0 => *v -= 1,
                    _ => return false,
                }
            }
        }
        true
    }
}
```

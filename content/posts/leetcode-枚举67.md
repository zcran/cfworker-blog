---
title: "leetcode-枚举67"
date: 2026-07-09T10:05:02+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 统计近似相等数对 I

给你一个正整数数组 nums 。

如果我们执行以下操作 至多一次 可以让两个整数 x 和 y 相等，那么我们称这个数对是 近似相等 的：

选择 x 或者 y  之一，将这个数字中的两个数位交换。
请你返回 nums 中，下标 i 和 j 满足 i < j 且 nums[i] 和 nums[j] 近似相等 的数对数目。

注意 ，执行操作后一个整数可以有前导 0 。


```
use std::collections::{HashMap, HashSet};

impl Solution {
    pub fn count_pairs(nums: Vec<i32>) -> i32 {
        // 排序使得相同数字相邻，便于去重计数
        let mut nums = nums;
        nums.sort_unstable();

        let mut ans = 0;
        let mut count: HashMap<i32, i32> = HashMap::new();

        for &x in &nums {
            // 生成所有近似相等的数字（包括原数字本身）
            let mut candidates = HashSet::new();
            candidates.insert(x);

            // 将数字转为字符串便于交换
            let s = x.to_string();
            let chars: Vec<char> = s.chars().collect();
            let m = chars.len();

            // 枚举所有可能的交换一次的情况
            for i in 0..m {
                for j in i + 1..m {
                    // 构造交换后的字符串
                    let mut swapped = chars.clone();
                    swapped.swap(i, j);
                    // 解析为整数（允许前导零）
                    if let Ok(val) = swapped.iter().collect::<String>().parse::<i32>() {
                        candidates.insert(val);
                    }
                }
            }

            // 统计所有候选值在前面的出现次数
            for &v in &candidates {
                if let Some(&cnt) = count.get(&v) {
                    ans += cnt;
                }
            }

            // 记录当前数字
            *count.entry(x).or_insert(0) += 1;
        }

        ans
    }
}
```

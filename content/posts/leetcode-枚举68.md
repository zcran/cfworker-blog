---
title: "leetcode-枚举68"
date: 2026-07-09T10:05:02+08:00
tags: ["leetcode", "枚举"]
draft: false
---


##  统计近似相等数对 II

注意：在这个问题中，操作次数增加为至多 两次 。

给你一个正整数数组 nums 。

如果我们执行以下操作 至多两次 可以让两个整数 x 和 y 相等，那么我们称这个数对是 近似相等 的：

选择 x 或者 y  之一，将这个数字中的两个数位交换。
请你返回 nums 中，下标 i 和 j 满足 i < j 且 nums[i] 和 nums[j] 近似相等 的数对数目。

注意 ，执行操作后得到的整数可以有前导 0 。


```
use std::collections::{HashMap, HashSet};

impl Solution {
    pub fn count_pairs(nums: Vec<i32>) -> i32 {
        let mut ans = 0;
        let mut count: HashMap<i32, i32> = HashMap::new();

        for &x in &nums {
            // 生成所有近似相等的数字（交换至多两次）
            let candidates = Self::generate_candidates(x);

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

    /// 生成数字 x 经过至多两次交换所有可能的结果
    fn generate_candidates(x: i32) -> HashSet<i32> {
        let mut candidates = HashSet::new();
        candidates.insert(x); // 不交换

        // 将数字转为固定长度 7 位的数字数组（题目范围保证不超过 7 位）
        let mut digits: [i32; 7] = [0; 7];
        let mut temp = x;
        for i in (0..7).rev() {
            digits[i] = temp % 10;
            temp /= 10;
        }

        // 辅助函数：将数字数组转回整数
        let to_number = |digits: &[i32; 7]| -> i32 {
            let mut ret = 0;
            for &d in digits {
                ret = ret * 10 + d;
            }
            ret
        };

        // 枚举第一次交换
        for i in 0..7 {
            for j in i + 1..7 {
                let mut after_first = digits;
                after_first.swap(i, j);
                candidates.insert(to_number(&after_first));

                // 枚举第二次交换
                for p in 0..7 {
                    for q in p + 1..7 {
                        let mut after_second = after_first;
                        after_second.swap(p, q);
                        candidates.insert(to_number(&after_second));
                    }
                }
            }
        }

        candidates
    }
}
```

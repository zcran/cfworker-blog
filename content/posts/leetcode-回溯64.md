---
title: "leetcode-回溯64"
date: 2026-07-04T10:22:04+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 一个小组的最大实力值

给你一个下标从 0 开始的整数数组 nums ，它表示一个班级中所有学生在一次考试中的成绩。老师想选出一部分同学组成一个 非空 小组，且这个小组的 实力值 最大，如果这个小组里的学生下标为 i0, i1, i2, ... , ik ，那么这个小组的实力值定义为 nums[i0] * nums[i1] * nums[i2] * ... * nums[ik​] 。

请你返回老师创建的小组能得到的最大实力值为多少。


```
impl Solution {
    pub fn max_strength(nums: Vec<i32>) -> i64 {
        let mut negatives = Vec::new();
        let mut positives = Vec::new();
        let mut zeros = 0;

        // 分类存储正数和负数
        for &num in &nums {
            if num < 0 {
                negatives.push(num);
            } else if num > 0 {
                positives.push(num);
            } else {
                zeros += 1;
            }
        }

        // 特殊情况：只有1个负数且没有正数
        if negatives.len() == 1 && positives.is_empty() {
            return if zeros > 0 { 0 } else { negatives[0] as i64 };
        }

        // 没有正数且负数数量<=1，只能选0（如果有0）或无法形成正数乘积
        if positives.is_empty() && negatives.len() <= 1 {
            return 0;
        }

        // 对负数排序，取绝对值最大的偶数个
        negatives.sort_unstable();
        let mut result: i64 = 1;

        // 如果负数个数为奇数，去掉绝对值最小的那个（最大的负数）
        let neg_count = if negatives.len() % 2 == 1 {
            negatives.len() - 1
        } else {
            negatives.len()
        };

        // 取最大的偶数个负数（绝对值最大的）
        for i in 0..neg_count {
            result *= negatives[i] as i64;
        }

        // 乘以所有正数
        for &p in &positives {
            result *= p as i64;
        }

        result
    }
}
```

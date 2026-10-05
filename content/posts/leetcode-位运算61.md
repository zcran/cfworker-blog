---
title: "leetcode-位运算61"
date: 2026-09-24T10:33:38+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 给定操作次数内使剩余元素的或值最小

给你一个下标从 0 开始的整数数组 nums 和一个整数 k 。

一次操作中，你可以选择 nums 中满足 0 <= i < nums.length - 1 的一个下标 i ，并将 nums[i] 和 nums[i + 1] 替换为数字 nums[i] & nums[i + 1] ，其中 & 表示按位 AND 操作。

请你返回 至多 k 次操作以内，使 nums 中所有剩余元素按位 OR 结果的 最小值 。


```
impl Solution {
    pub fn min_or_after_operations(nums: Vec<i32>, k: i32) -> i32 {
        let mut ans = 0;
        let mut mask = 0;

        // 从高位到低位逐位确定答案
        for b in (0..30).rev() {
            mask |= 1 << b;
            let mut cnt = 0;
            let mut and_res = -1; // -1 表示所有位全为 1

            // 贪心合并：若当前段 AND 结果非零，说明该段无法通过合并清除当前位
            for &x in &nums {
                and_res &= x & mask;
                if and_res != 0 {
                    cnt += 1; // 需要一次合并操作
                } else {
                    and_res = -1; // 当前段已清零，开始新的一段
                }
            }

            // 若操作次数超过 k，则答案该位必须为 1，并从 mask 中移除该位
            if cnt > k {
                ans |= 1 << b;
                mask ^= 1 << b;
            }
        }

        ans
    }
}
```

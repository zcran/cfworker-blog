---
title: "leetcode-枚举40"
date: 2026-07-09T10:05:01+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 所有子数组中不平衡数字之和

一个长度为 n 下标从 0 开始的整数数组 arr 的 不平衡数字 定义为，在 sarr = sorted(arr) 数组中，满足以下条件的下标数目：

0 <= i < n - 1 ，和
sarr[i+1] - sarr[i] > 1

这里，sorted(arr) 表示将数组 arr 排序后得到的数组。

给你一个下标从 0 开始的整数数组 nums ，请你返回它所有 子数组 的 不平衡数字 之和。

子数组指的是一个数组中连续一段 非空 的元素序列。


```
impl Solution {
    pub fn sum_imbalance_numbers(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        let mut ans = 0;

        // 遍历每个子数组的起始位置
        for i in 0..n {
            // 使用 Vec<bool> 记录数字是否出现过，大小 n+2 因为元素在 1..n 之间
            // 用 u8 或 bool 都可以，这里用 Vec<bool> 但注意它可能不是位压缩的
            let mut seen = vec![false; n + 2];
            seen[nums[i] as usize] = true;

            // cnt 表示当前子数组 [i..j] 的不平衡数
            let mut cnt = 0;

            // 扩展子数组的右边界
            for j in i + 1..n {
                let x = nums[j] as usize;

                if !seen[x] {
                    // 插入 x 后，不平衡数的变化：
                    // 如果 x-1 存在，则原本 x-1 和 x 之间的间隔被填补，不平衡数减 1
                    // 如果 x+1 存在，则原本 x 和 x+1 之间的间隔被填补，不平衡数减 1
                    // 如果 x-1 和 x+1 都不存在，则新元素自身形成一个孤立点，不平衡数加 1
                    // 所以净变化为：1 - (x-1存在) - (x+1存在)
                    let left_exists = seen[x - 1];
                    let right_exists = seen[x + 1];
                    cnt += 1 - (left_exists as i32) - (right_exists as i32);
                    seen[x] = true;
                }

                ans += cnt;
            }
        }

        ans
    }
}
```

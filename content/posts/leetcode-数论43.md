---
title: "leetcode-数论43"
date: 2026-06-20T11:09:56+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 通过操作使数组长度最小

给你一个下标从 0 开始的整数数组 nums ，它只包含 正 整数。

你的任务是通过进行以下操作 任意次 （可以是 0 次） 最小化 nums 的长度：

· 在 nums 中选择 两个不同 的下标 i 和 j ，满足 nums[i] > 0 且 nums[j] > 0 。
· 将结果 nums[i] % nums[j] 插入 nums 的结尾。
· 将 nums 中下标为 i 和 j 的元素删除。

请你返回一个整数，它表示进行任意次操作以后 nums 的 最小长度 。


```
impl Solution {
    pub fn minimum_array_length(nums: Vec<i32>) -> i32 {
        // 找到数组中的最小值
        let min_val = *nums.iter().min().unwrap();

        // 检查是否有任何元素不能被最小值整除
        // 如果存在 x % min_val != 0，则可以通过一次操作得到更小的数
        for &x in &nums {
            if x % min_val != 0 {
                return 1;
            }
        }

        // 计算最小值出现的次数
        let min_count = nums.iter().filter(|&&x| x == min_val).count();

        // 返回 (count + 1) / 2
        ((min_count + 1) / 2) as i32
    }
}
```

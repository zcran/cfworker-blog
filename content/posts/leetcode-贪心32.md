---
title: "leetcode-贪心32"
date: 2026-10-03T12:55:36+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## K 次取反后最大化的数组和

给你一个整数数组 nums 和一个整数 k ，按以下方法修改该数组：

选择某个下标 i 并将 nums[i] 替换为 -nums[i] 。

重复这个过程恰好 k 次。可以多次选择同一个下标 i 。

以这种方式修改数组后，返回数组 可能的最大和 。


```
impl Solution {
    pub fn largest_sum_after_k_negations(mut nums: Vec<i32>, mut k: i32) -> i32 {
        // 排序后从左到右处理负数（等价于优先取反绝对值大的负数）
        nums.sort_unstable();

        let mut sum = 0;
        let mut min_abs = i32::MAX;

        for x in nums.iter_mut() {
            if k > 0 && *x < 0 {
                *x = -*x;
                k -= 1;
            }
            sum += *x;
            min_abs = min_abs.min(*x);
        }

        // 若剩余取反次数为奇数，取反绝对值最小的元素使损失最小
        if k % 2 == 1 {
            sum -= min_abs * 2;
        }

        sum
    }
}
```

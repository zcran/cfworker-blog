---
title: "leetcode-贪心103"
date: 2026-10-03T12:55:41+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 将数组排序的最少替换次数

给你一个下标从 0 开始的整数数组 nums 。每次操作中，你可以将数组中任何一个元素替换为 任意两个 和为该元素的数字。

比方说，nums = [5,6,7] 。一次操作中，我们可以将 nums[1] 替换成 2 和 4 ，将 nums 转变成 [5,2,4,7] 。

请你执行上述操作，将数组变成元素按 非递减 顺序排列的数组，并返回所需的最少操作次数。


```
impl Solution {
    pub fn minimum_replacement(mut nums: Vec<i32>) -> i64 {
        // 从右向左处理：保证每个元素拆分后的最大值不超过其右侧元素的最小值。
        // 对当前值 v，若 v > 右侧上限 limit，则需要拆成 x 份，
        // 使每份最大值尽量大且不超过 limit，即 x = ceil(v / limit)。
        // 拆分后当前元素能提供的最大份值为 v / x，作为新的 limit。
        // 操作次数增加 x - 1。
        nums.reverse();
        let mut ops = 0i64;
        let mut limit = nums[0];

        for &v in nums.iter().skip(1) {
            if v > limit {
                let parts = (v + limit - 1) / limit; // ceil(v / limit)
                limit = v / parts;
                ops += (parts - 1) as i64;
            } else {
                limit = v;
            }
        }
        ops
    }
}
```

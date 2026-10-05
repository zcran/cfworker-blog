---
title: "leetcode-贪心38"
date: 2026-10-03T12:55:36+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 递减元素使数组呈锯齿状

给你一个整数数组 nums，每次 操作 会从中选择一个元素并 将该元素的值减少 1。

如果符合下列情况之一，则数组 A 就是 锯齿数组：

每个偶数索引对应的元素都大于相邻的元素，即 A[0] > A[1] < A[2] > A[3] < A[4] > ...

或者，每个奇数索引对应的元素都大于相邻的元素，即 A[0] < A[1] > A[2] < A[3] > A[4] < ...

返回将数组 nums 转换为锯齿数组所需的最小操作次数。


```
impl Solution {
    pub fn moves_to_make_zigzag(nums: Vec<i32>) -> i32 {
        // 对每个位置，使其小于两侧相邻元素所需的最小减少量
        // 若两侧最小值已比当前值小，则无需操作；否则需减少到 min(左右) - 1
        let cost = |i: usize, nums: &[i32]| -> i32 {
            let left  = if i > 0 { nums[i - 1] } else { i32::MAX };
            let right = if i + 1 < nums.len() { nums[i + 1] } else { i32::MAX };
            0.max(nums[i] - left.min(right) + 1)
        };

        // 分别计算两种锯齿模式（偶数位为峰 / 奇数位为峰）所需操作数
        let (mut even, mut odd) = (0, 0);
        for i in 0..nums.len() {
            if i % 2 == 0 {
                even += cost(i, &nums);
            } else {
                odd += cost(i, &nums);
            }
        }

        even.min(odd)
    }
}
```

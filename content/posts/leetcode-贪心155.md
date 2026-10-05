---
title: "leetcode-贪心155"
date: 2026-10-03T12:55:44+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 数组元素相等转换

给你一个大小为 n 的整数数组 nums，其中只包含 1 和 -1，以及一个整数 k。

你可以最多进行 k 次以下操作：

选择一个下标 i（0 <= i < n - 1），然后将 nums[i] 和 nums[i + 1] 同时 乘以 -1。

注意：你可以在 不同 的操作中多次选择相同的下标 i。

如果在最多 k 次操作后可以使数组的所有元素相等，则返回 true；否则，返回 false。


```
impl Solution {
    pub fn can_make_equal(nums: Vec<i32>, k: i32) -> bool {
        // 目标：把数组全部变成 target（1 或 -1），看能否在 k 次内完成。
        // 从左到右扫描，用 mul 表示当前元素因前一次操作被翻转的符号（1 或 -1）。
        // 若 nums[i] * mul != target，则必须在下标 i 上做一次操作，
        // 使 nums[i] 和 nums[i+1] 都乘以 -1，于是下一个元素的 mul 变为 -1。
        let check = |target: i32| -> bool {
            let mut left = k;
            let mut mul = 1;
            let n = nums.len();
            for i in 0..n {
                if nums[i] * mul == target {
                    mul = 1;
                    continue;
                }
                // 需要操作，但不能越界或次数不足
                if left == 0 || i + 1 == n {
                    return false;
                }
                left -= 1;
                mul = -1;
            }
            true
        };

        check(1) || check(-1)
    }
}
```

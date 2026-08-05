---
title: "leetcode-滑动窗口109"
date: 2026-07-18T11:02:35+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 使数组平衡的最少移除数目

给你一个整数数组 nums 和一个整数 k。

如果一个数组的 最大 元素的值 至多 是其 最小 元素的 k 倍，则该数组被称为是 平衡 的。

你可以从 nums 中移除 任意 数量的元素，但不能使其变为 空 数组。

返回为了使剩余数组平衡，需要移除的元素的 最小 数量。

注意：大小为 1 的数组被认为是平衡的，因为其最大值和最小值相等，且条件总是成立。


```
impl Solution {
    pub fn min_removal(mut nums: Vec<i32>, k: i32) -> i32 {
        // 排序后，平衡子数组必然是排序数组中的一个连续区间
        nums.sort_unstable();

        let n = nums.len();
        let k = k as i64;
        let mut max_len = 0;
        let mut right = 0;

        // 滑动窗口：固定左端点，扩展右端点直到不满足条件
        for left in 0..n {
            // 扩展右指针，使窗口内满足 nums[right] <= nums[left] * k
            while right < n && nums[right] as i64 <= nums[left] as i64 * k {
                right += 1;
            }
            // 当前窗口长度 right - left 就是保留的元素数
            max_len = max_len.max(right - left);
        }

        // 最少移除数 = 总数 - 最大保留数
        n as i32 - max_len as i32
    }
}
```

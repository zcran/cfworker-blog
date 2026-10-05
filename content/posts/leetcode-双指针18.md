---
title: "leetcode-双指针18"
date: 2026-09-05T09:53:01+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 移动零

给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。

请注意 ，必须在不复制数组的情况下原地对数组进行操作。


```
impl Solution {
    /// 将数组中所有 0 移动到末尾（双指针法）
    /// 时间复杂度 O(n)，空间复杂度 O(1)
    /// 慢指针记录下一个非零元素的位置，快指针遍历数组
    pub fn move_zeroes(nums: &mut Vec<i32>) {
        let mut write = 0;

        // 将所有非零元素依次移到数组前部
        for read in 0..nums.len() {
            if nums[read] != 0 {
                if read != write {
                    nums[write] = nums[read];
                }
                write += 1;
            }
        }

        // 剩余位置填充 0
        nums[write..].fill(0);
    }
}
```

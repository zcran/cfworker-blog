---
title: "leetcode-单调栈26"
date: 2026-06-07T10:14:05+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 删除最短的子数组使剩余数组有序

给你一个整数数组 arr ，请你删除一个子数组（可以为空），使得 arr 中剩下的元素是 非递减 的。

一个子数组指的是原数组中连续的一个子序列。

请你返回满足题目要求的最短子数组的长度。

```
impl Solution {
    /// 双指针优化版本
    /// 时间复杂度 O(n)  空间复杂度 O(1)
    ///
    /// 核心优化：用双指针代替二分查找，实现 O(n)
    pub fn find_length_of_shortest_subarray(arr: Vec<i32>) -> i32 {
        let n = arr.len();

        // 找左边界
        let mut left = 0;
        while left + 1 < n && arr[left] <= arr[left + 1] {
            left += 1;
        }
        if left == n - 1 { return 0; }

        // 找右边界
        let mut right = n - 1;
        while right > 0 && arr[right - 1] <= arr[right] {
            right -= 1;
        }

        // 初始答案
        let mut ans = (n - left - 1).min(right);

        // 双指针合并：i 遍历前缀，j 遍历后缀
        let mut j = right;
        for i in 0..=left {
            // 移动 j 使 arr[j] >= arr[i]
            while j < n && arr[j] < arr[i] {
                j += 1;
            }
            // 删除 (i+1..j-1) 部分
            ans = ans.min(j - i - 1);
        }

        ans as i32
    }
}
```

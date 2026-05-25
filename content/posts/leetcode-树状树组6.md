---
title: "leetcode-树状树组6"
date: 2026-05-03T20:07:52+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 翻转对

给定一个数组 nums ，如果 i < j 且 nums[i] > 2*nums[j] 我们就将 (i, j) 称作一个重要翻转对。

你需要返回给定数组中的重要翻转对的数量。

示例 1:

输入: [1,3,2,3,1]
输出: 2
示例 2:

输入: [2,4,3,5,1]
输出: 3

注意:

·给定数组的长度不会超过50000。
·输入数组中的所有数字都在32位整数的表示范围内。

```
impl Solution {
    /// 计算逆序对变种：i < j 且 nums[i] > 2 * nums[j] 的对数
    /// 采用归并排序，在合并阶段统计跨越左右两边的翻转对
    pub fn reverse_pairs(nums: Vec<i32>) -> i32 {
        let mut nums = nums;
        let len = nums.len();
        if len == 0 {
            return 0;
        }
        let mut buffer = vec![0; len]; // 辅助数组，避免重复分配
        Self::merge_sort_and_count(&mut nums, &mut buffer, 0, len - 1) as i32
    }

    /// 归并排序并统计翻转对数量
    /// 返回当前区间 [left, right] 内的翻转对个数
    fn merge_sort_and_count(nums: &mut [i32], buffer: &mut [i32], left: usize, right: usize) -> usize {
        if left >= right {
            return 0;
        }
        let mid = left + (right - left) / 2;
        // 分治统计左右两半内部的翻转对
        let mut count = Self::merge_sort_and_count(nums, buffer, left, mid)
            + Self::merge_sort_and_count(nums, buffer, mid + 1, right);

        // 统计左半元素与右半元素构成的翻转对 (nums[i] > 2 * nums[j])
        let (mut i, mut j) = (left, mid + 1);
        while i <= mid && j <= right {
            if nums[i] as i64 > 2 * nums[j] as i64 {
                // 左半从 i 到 mid 都满足条件（因为左半有序递增）
                count += mid - i + 1;
                j += 1;
            } else {
                i += 1;
            }
        }

        // 合并两个有序子数组（标准归并）
        Self::merge(nums, buffer, left, mid, right);
        count
    }

    /// 合并 [left, mid] 和 [mid+1, right] 两个有序子数组，并写回 nums
    fn merge(nums: &mut [i32], buffer: &mut [i32], left: usize, mid: usize, right: usize) {
        let (mut i, mut j) = (left, mid + 1);
        let mut pos = 0; // buffer 中的写入位置

        // 双指针合并
        while i <= mid && j <= right {
            if nums[i] <= nums[j] {
                buffer[pos] = nums[i];
                i += 1;
            } else {
                buffer[pos] = nums[j];
                j += 1;
            }
            pos += 1;
        }
        // 剩余元素拷贝
        while i <= mid {
            buffer[pos] = nums[i];
            pos += 1;
            i += 1;
        }
        while j <= right {
            buffer[pos] = nums[j];
            pos += 1;
            j += 1;
        }
        // 将合并结果写回原数组
        nums[left..=right].copy_from_slice(&buffer[..pos]);
    }
}
```

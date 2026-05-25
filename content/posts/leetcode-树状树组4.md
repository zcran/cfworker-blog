---
title: "leetcode-树状树组4"
date: 2026-05-03T20:07:52+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 区间和的个数

给你一个整数数组 nums 以及两个整数 lower 和 upper 。求数组中，值位于范围 [lower, upper] （包含 lower 和 upper）之内的 区间和的个数 。

区间和 S(i, j) 表示在 nums 中，位置从 i 到 j 的元素之和，包含 i 和 j (i ≤ j)。

```
impl Solution {
    /// 计算数组 `nums` 中所有满足 `lower <= 子数组和 <= upper` 的子数组个数
    /// 时间复杂度 O(n log n)，空间复杂度 O(n)
    pub fn count_range_sum(nums: Vec<i32>, lower: i32, upper: i32) -> i32 {
        // 转换为 i64 防止溢出，并计算前缀和
        let mut prefix_sums: Vec<i64> = nums
            .into_iter()
            .scan(0i64, |sum, x| {
                *sum += x as i64;
                Some(*sum)
            })
            .collect();
        // 前缀和数组前方插入 0，便于表示从第一个元素开始的子数组
        prefix_sums.insert(0, 0);

        let mut count = 0;
        Self::merge_sort_and_count(&mut prefix_sums, lower as i64, upper as i64, &mut count);
        count
    }

    /// 归并排序（分治），同时统计满足条件的区间和
    /// 采用滑动窗口在合并阶段计数，复杂度 O(n)
    fn merge_sort_and_count(nums: &mut [i64], lower: i64, upper: i64, count: &mut i32) {
        let len = nums.len();
        if len <= 1 {
            return;
        }

        let mid = len / 2;
        Self::merge_sort_and_count(&mut nums[..mid], lower, upper, count);
        Self::merge_sort_and_count(&mut nums[mid..], lower, upper, count);
        Self::merge_and_count(nums, mid, lower, upper, count);
    }

    /// 合并两个有序子数组 [0..mid) 和 [mid..len)，
    /// 同时对左子数组的每个元素，统计右子数组中满足差值在 [lower, upper] 的元素个数
    fn merge_and_count(nums: &mut [i64], mid: usize, lower: i64, upper: i64, count: &mut i32) {
        // 滑动窗口：对于每个左元素 nums[i]，右区间 [start, end) 内的元素满足条件
        let (mut start, mut end) = (mid, mid);
        for i in 0..mid {
            let left_val = nums[i];
            // 扩大 start 直到 nums[start] - left_val >= lower
            while start < nums.len() && nums[start] - left_val < lower {
                start += 1;
            }
            // 扩大 end 直到 nums[end] - left_val > upper
            while end < nums.len() && nums[end] - left_val <= upper {
                end += 1;
            }
            // 当前左元素对应的合法右元素个数为 (end - start)
            *count += (end - start) as i32;
        }

        // 归并两个有序子数组（标准合并过程）
        let mut merged = Vec::with_capacity(nums.len());
        let (mut i, mut j) = (0, mid);
        while i < mid && j < nums.len() {
            if nums[i] <= nums[j] {
                merged.push(nums[i]);
                i += 1;
            } else {
                merged.push(nums[j]);
                j += 1;
            }
        }
        // 将剩余元素添加到合并结果中
        merged.extend_from_slice(&nums[i..mid]);
        merged.extend_from_slice(&nums[j..]);

        // 将合并后的有序数组写回原切片
        nums.copy_from_slice(&merged);
    }
}
```

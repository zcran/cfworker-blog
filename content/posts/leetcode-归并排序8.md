---
title: "Leetcode 归并排序8"
date: 2025-05-23T21:22:00+08:00
tags: ["leetcode", "归并排序"]
draft: false
---

## 满足不等式的数对数目

给你两个下标从 0 开始的整数数组 nums1 和 nums2 ，两个数组的大小都为 n ，同时给你一个整数 diff ，统计满足以下条件的 数对 (i, j) ：

0 <= i < j <= n - 1 且
nums1[i] - nums1[j] <= nums2[i] - nums2[j] + diff.
请你返回满足条件的 数对数目 。

示例 1：
输入：nums1 = [3,2,5], nums2 = [2,2,1], diff = 1
输出：3
解释：
总共有 3 个满足条件的数对：
1. i = 0, j = 1：3 - 2 <= 2 - 2 + 1 。因为 i < j 且 1 <= 1 ，这个数对满足条件。
2. i = 0, j = 2：3 - 5 <= 2 - 1 + 1 。因为 i < j 且 -2 <= 2 ，这个数对满足条件。
3. i = 1, j = 2：2 - 5 <= 2 - 1 + 1 。因为 i < j 且 -3 <= 2 ，这个数对满足条件。
所以，我们返回 3 。

示例 2：
输入：nums1 = [3,-1], nums2 = [-2,2], diff = -1
输出：0
解释：
没有满足条件的任何数对，所以我们返回 0 。

```
impl Solution {
    pub fn number_of_pairs(nums1: Vec<i32>, nums2: Vec<i32>, diff: i32) -> i64 {
        // 计算差值数组
        let mut nums = nums1.into_iter()
            .zip(nums2.into_iter())
            .map(|(x, y)| x - y)
            .collect::<Vec<_>>();
        
        // 提前计算数组长度
        let len = nums.len();
        if len == 0 {
            return 0;
        }
        
        // 用于存储归并排序过程中的临时数组
        let mut temp = vec![0; len];
        // 执行归并排序并统计满足条件的数对
        Self::merge_sort_and_count(&mut nums, &mut temp, 0, len - 1, diff)
    }
    
    fn merge_sort_and_count(nums: &mut [i32], temp: &mut [i32], left: usize, right: usize, diff: i32) -> i64 {
        if left >= right {
            return 0;
        }
        
        let mid = left + (right - left) / 2;
        // 递归处理左右子数组并累加满足条件的数对
        let mut count = Self::merge_sort_and_count(nums, temp, left, mid, diff)
            + Self::merge_sort_and_count(nums, temp, mid + 1, right, diff);
        
        // 统计跨越中点的满足条件的数对
        let mut j = mid + 1;
        for i in left..=mid {
            // 找到右半部分第一个满足 nums[i] - nums[j] > diff 的位置
            while j <= right && nums[i] - nums[j] > diff {
                j += 1;
            }
            // 累加满足条件的数对数目
            count += (right - j + 1) as i64;
        }
        
        // 合并两个有序子数组
        Self::merge(nums, temp, left, mid, right);
        
        count
    }
    
    fn merge(nums: &mut [i32], temp: &mut [i32], left: usize, mid: usize, right: usize) {
        // 复制当前合并范围的元素到临时数组
        temp[left..=right].copy_from_slice(&nums[left..=right]);
        
        let mut i = left;
        let mut j = mid + 1;
        let mut k = left;
        
        // 合并两个有序子数组
        while i <= mid && j <= right {
            if temp[i] <= temp[j] {
                nums[k] = temp[i];
                i += 1;
            } else {
                nums[k] = temp[j];
                j += 1;
            }
            k += 1;
        }
        
        // 复制左半部分剩余元素
        while i <= mid {
            nums[k] = temp[i];
            i += 1;
            k += 1;
        }
        
        // 复制右半部分剩余元素
        while j <= right {
            nums[k] = temp[j];
            j += 1;
            k += 1;
        }
    }
}
```
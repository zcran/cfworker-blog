---
title: "leetcode-双指针51"
date: 2026-09-05T09:53:03+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 优势洗牌

给定两个长度相等的数组 nums1 和 nums2，nums1 相对于 nums2 的优势可以用满足 nums1[i] > nums2[i] 的索引 i 的数目来描述。

返回 nums1 的 任意 排列，使其相对于 nums2 的优势最大化。




```
impl Solution {
    pub fn advantage_count(mut nums1: Vec<i32>, nums2: Vec<i32>) -> Vec<i32> {
        let n = nums1.len();

        // 对 nums1 排序，用于贪心匹配
        nums1.sort_unstable();

        // 创建 nums2 的索引数组，按 nums2 的值降序排序
        let mut idx: Vec<usize> = (0..n).collect();
        idx.sort_unstable_by(|&a, &b| nums2[b].cmp(&nums2[a]));

        let mut result = vec![0; n];
        let mut left = 0;      // nums1 中最小值的指针
        let mut right = n - 1; // nums1 中最大值的指针

        // 贪心策略：优先用较大的值击败 nums2 中较大的值
        for &i in &idx {
            if nums1[right] > nums2[i] {
                // 当前最大值可以击败 nums2[i]，使用它
                result[i] = nums1[right];
                right -= 1;
            } else {
                // 当前最大值无法击败，用最小值填充（田忌赛马）
                result[i] = nums1[left];
                left += 1;
            }
        }

        result
    }
}
```

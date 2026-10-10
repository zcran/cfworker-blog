---
title: "leetcode-动态规划104"
date: 2026-10-05T20:40:01+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 拼接数组的最大分数

给你两个下标从 0 开始的整数数组 nums1 和 nums2 ，长度都是 n 。

你可以选择两个整数 left 和 right ，其中 0 <= left <= right < n ，接着 交换 两个子数组 nums1[left...right] 和 nums2[left...right] 。

例如，设 nums1 = [1,2,3,4,5] 和 nums2 = [11,12,13,14,15] ，整数选择 left = 1 和 right = 2，那么 nums1 会变为 [1,12,13,4,5] 而 nums2 会变为 [11,2,3,14,15] 。

你可以选择执行上述操作 一次 或不执行任何操作。

数组的 分数 取 sum(nums1) 和 sum(nums2) 中的最大值，其中 sum(arr) 是数组 arr 中所有元素之和。

返回 可能的最大分数 。

子数组 是数组中连续的一个元素序列。arr[left...right] 表示子数组包含 nums 中下标 left 和 right 之间的元素（含 下标 left 和 right 对应元素）。


```
impl Solution {
    pub fn maximums_spliced_array(nums1: Vec<i32>, nums2: Vec<i32>) -> i32 {
        // 计算把 nums2 的一段换到 nums1 后，nums1 能达到的最大和
        // 相当于在差值数组 (nums2[i] - nums1[i]) 上求最大子数组和
        fn best_sum(a: &[i32], b: &[i32]) -> i32 {
            let base: i32 = a.iter().sum();
            let mut cur = 0;      // 以当前位置结尾的最大差值子数组和
            let mut best = 0;     // 全局最大差值子数组和（允许空）
            for (x, y) in a.iter().zip(b.iter()) {
                let diff = y - x;
                cur = diff.max(cur + diff);
                best = best.max(cur);
            }
            base + best
        }

        // 两次交换方向对称，取较大者
        best_sum(&nums1, &nums2).max(best_sum(&nums2, &nums1))
    }
}
```

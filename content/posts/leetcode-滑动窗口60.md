---
title: "leetcode-滑动窗口60"
date: 2026-07-18T11:02:32+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 按位或最大的最小子数组长度

给你一个长度为 n 下标从 0 开始的数组 nums ，数组中所有数字均为非负整数。对于 0 到 n - 1 之间的每一个下标 i ，你需要找出 nums 中一个 最小 非空子数组，它的起始位置为 i （包含这个位置），同时有 最大 的 按位或运算值 。

换言之，令 Bij 表示子数组 nums[i...j] 的按位或运算的结果，你需要找到一个起始位置为 i 的最小子数组，这个子数组的按位或运算的结果等于 max(Bik) ，其中 i <= k <= n - 1 。
一个数组的按位或运算值是这个数组里所有数字按位或运算的结果。

请你返回一个大小为 n 的整数数组 answer，其中 answer[i]是开始位置为 i ，按位或运算结果最大，且 最短 子数组的长度。

子数组 是数组里一段连续非空元素组成的序列。


```
impl Solution {
    pub fn smallest_subarrays(nums: Vec<i32>) -> Vec<i32> {
        let n = nums.len();
        // last_pos[bit] 记录在当前索引之后，bit位首次出现1的位置
        let mut last_pos = vec![-1; 32];
        let mut ans = vec![1; n];

        // 从右向左遍历
        for i in (0..n).rev() {
            let mut furthest = i;

            // 更新当前位置的每一位
            for bit in 0..32 {
                if (nums[i] >> bit) & 1 == 1 {
                    last_pos[bit] = i as i32;
                } else {
                    // 如果当前位是0，但右边某位置有1，需要包含那个位置
                    if last_pos[bit] != -1 {
                        furthest = furthest.max(last_pos[bit] as usize);
                    }
                }
            }

            // 需要覆盖所有出现过的1位，所以子数组长度至少为 furthest - i + 1
            ans[i] = (furthest - i + 1) as i32;
        }

        ans
    }
}
```

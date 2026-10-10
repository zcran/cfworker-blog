---
title: "leetcode-排序58"
date: 2026-10-05T00:35:03+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 合法分割的最小下标

如果在长度为 m 的整数数组 arr 中 超过一半 的元素值为 x，那么我们称 x 是 支配元素 。

给你一个下标从 0 开始长度为 n 的整数数组 nums ，数据保证它含有一个 支配 元素。

你需要在下标 i 处将 nums 分割成两个数组 nums[0, ..., i] 和 nums[i + 1, ..., n - 1] ，如果一个分割满足以下条件，我们称它是 合法 的：

0 <= i < n - 1
nums[0, ..., i] 和 nums[i + 1, ..., n - 1] 的支配元素相同。

这里， nums[i, ..., j] 表示 nums 的一个子数组，它开始于下标 i ，结束于下标 j ，两个端点都包含在子数组内。特别地，如果 j < i ，那么 nums[i, ..., j] 表示一个空数组。

请你返回一个 合法分割 的 最小 下标。如果合法分割不存在，返回 -1 。


```
impl Solution {
    /// 返回合法分割的最小下标，不存在则返回 -1。
    ///
    /// 思路：
    /// 1. 先找出整个数组的支配元素 mode 及其总出现次数 total。
    /// 2. 从左到右扫描分割点 i，用 freq1 记录 mode 在左半部分出现的次数。
    /// 3. 左半部分长度为 i+1，右半部分长度为 n-i-1。
    ///    合法条件为：freq1 * 2 > i+1 且 (total - freq1) * 2 > n-i-1。
    /// 4. 找到的第一个满足条件的分割点即为最小下标。
    pub fn minimum_index(nums: Vec<i32>) -> i32 {
        let n = nums.len();

        // 统计整个数组的支配元素及其出现次数
        let mut freq = std::collections::HashMap::new();
        let mut mode = nums[0];
        let mut max_cnt = 0;
        for &x in &nums {
            let c = freq.entry(x).or_insert(0);
            *c += 1;
            if *c > max_cnt {
                max_cnt = *c;
                mode = x;
            }
        }

        // 扫描分割点，判断左右两半是否都以 mode 为支配元素
        let mut left_cnt = 0;
        for i in 0..n - 1 {
            if nums[i] == mode {
                left_cnt += 1;
            }
            let right_cnt = max_cnt - left_cnt;
            if left_cnt * 2 > (i + 1) as i32 && right_cnt * 2 > (n - i - 1) as i32 {
                return i as i32;
            }
        }

        -1
    }
}
```

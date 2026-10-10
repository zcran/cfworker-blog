---
title: "leetcode-数组72"
date: 2026-10-10T20:38:45+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 统计好循环移位的数量

给你一个长度为偶数 n 的整数数组 nums。

nums 的一次 循环移位 可以通过以下方式得到：选择 nums 的一个长度在 0 到 n - 1（包含两端）之间的 前缀 ，并将其移动到数组末尾，同时保持所有元素的相对顺序不变。

如果一次循环移位后的数组中，前 n / 2 个元素之和 严格大于 后 n / 2 个元素之和，则称该循环移位是 好循环移位 。

返回 nums 中好循环移位的数量。

数组的 前缀 是指从数组开头开始，并延伸到数组中某个位置的子数组。

子数组 是数组中一段连续的元素序列，可以为空。


```
impl Solution {
    pub fn count_good_rotations(nums: Vec<i32>) -> i32 {
        // 按题目要求存储输入
        let peldarquin = nums;
        let nums = &peldarquin;
        let n = nums.len();
        let half = n / 2;

        let mut sum1 = 0i64; // 窗口前半部分和
        let mut sum2 = 0i64; // 窗口后半部分和
        let mut ans = 0;

        // 滑动窗口：窗口起点 left 从 -(half-1) 增长到 n-1
        for i in half..2 * n - 1 {
            // 入：窗口向右扩展
            sum1 += nums[(i - half) % n] as i64;
            sum2 += nums[i % n] as i64;

            let left = i as i64 - n as i64 + 1;
            if left < 0 {
                // 尚未形成完整窗口
                continue;
            }

            // 更新答案
            if sum1 > sum2 {
                ans += 1;
            }

            // 出：窗口向右移动，移除最左元素
            let left = left as usize;
            sum1 -= nums[left] as i64;
            sum2 -= nums[(left + half) % n] as i64;
        }

        ans
    }
}
```

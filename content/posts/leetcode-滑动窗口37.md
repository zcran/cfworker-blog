---
title: "leetcode-滑动窗口37"
date: 2026-07-18T11:02:31+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 删掉一个元素以后全为 1 的最长子数组

给你一个二进制数组 nums ，你需要从中删掉一个元素。

请你在删掉元素的结果数组中，返回最长的且只包含 1 的非空子数组的长度。

如果不存在这样的子数组，请返回 0 。


```
impl Solution {
    pub fn longest_subarray(nums: Vec<i32>) -> i32 {
        let mut left = 0;
        let mut zero_count = 0;
        let mut max_len = 0;

        // 滑动窗口：允许最多一个 0
        for right in 0..nums.len() {
            if nums[right] == 0 {
                zero_count += 1;
            }

            // 如果窗口内 0 的数量超过 1，移动左指针
            while zero_count > 1 {
                if nums[left] == 0 {
                    zero_count -= 1;
                }
                left += 1;
            }

            // 窗口长度为 right - left + 1，但必须删除一个元素
            // 如果窗口全是 1，删除一个 1 后长度为 right - left
            // 如果窗口有一个 0，删除那个 0 后长度为 right - left
            let cur_len = right - left; // 删除一个元素后的长度
            max_len = max_len.max(cur_len);
        }

        max_len as i32
    }
}
```

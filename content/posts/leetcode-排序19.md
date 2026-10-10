---
title: "leetcode-排序19"
date: 2026-10-05T00:35:00+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 有序队列

给定一个字符串 s 和一个整数 k 。你可以从 s 的前 k 个字母中选择一个，并把它加到字符串的末尾。

返回 在应用上述步骤的任意数量的移动后，字典序最小的字符串 。


```
impl Solution {
    /// 移动前 k 个字母中的任意一个到末尾，可任意次，返回字典序最小的字符串
    ///
    /// 思路：
    ///   - k > 1：任意相邻两字符都能通过移动交换，等价于可任意重排，直接升序排列。
    ///   - k == 1：只能把首字符移到末尾，即整体循环左移，答案是 s 的所有循环同构串中最小者。
    /// 时间 O(n log n)，额外空间 O(n)。
    pub fn orderly_queue(s: String, k: i32) -> String {
        if k > 1 {
            // 可任意重排：升序即为最小
            let mut bytes = s.into_bytes();
            bytes.sort_unstable();
            return unsafe { String::from_utf8_unchecked(bytes) };
        }

        // k == 1：枚举所有循环左移，取字典序最小
        let n = s.len();
        let doubled = s.repeat(2);
        // 所有长度为 n 的窗口即全部循环同构串
        (0..n).map(|i| &doubled[i..i + n]).min().unwrap().to_owned()
    }
}
```

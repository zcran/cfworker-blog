---
title: "leetcode-滑动窗口94"
date: 2026-07-18T11:02:34+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 统计满足 K 约束的子字符串数量 I

给你一个 二进制 字符串 s 和一个整数 k。

如果一个 二进制字符串 满足以下任一条件，则认为该字符串满足 k 约束：

字符串中 0 的数量最多为 k。
字符串中 1 的数量最多为 k。

返回一个整数，表示 s 的所有满足 k 约束 的子字符串的数量。


```
impl Solution {
    pub fn count_k_constraint_substrings(s: String, k: i32) -> i32 {
        let s = s.as_bytes();
        let n = s.len();
        let k = k as usize;
        let mut ans = 0;
        let mut freq = [0, 0]; // freq[0] = 0的数量, freq[1] = 1的数量
        let mut left = 0;

        // 滑动窗口：右指针扩展
        for right in 0..n {
            // 1. 将右端字符加入窗口
            let ch = (s[right] - b'0') as usize;
            freq[ch] += 1;

            // 2. 当窗口既不满足0 <= k，也不满足1 <= k时，收缩左指针
            while freq[0] > k && freq[1] > k {
                let left_ch = (s[left] - b'0') as usize;
                freq[left_ch] -= 1;
                left += 1;
            }

            // 3. 以right结尾的所有子数组都满足约束
            // 子数组起始位置可以是 left..=right，共 (right - left + 1) 个
            ans += (right - left + 1) as i32;
        }

        ans
    }
}
```

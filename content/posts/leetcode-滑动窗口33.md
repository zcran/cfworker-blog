---
title: "leetcode-滑动窗口33"
date: 2026-07-18T11:02:31+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 包含所有三种字符的子字符串数目

给你一个字符串 s ，它只包含三种字符 a, b 和 c 。

请你返回 a，b 和 c 都 至少 出现过一次的子字符串数目。


```
impl Solution {
    pub fn number_of_substrings(s: String) -> i32 {
        let s = s.as_bytes();
        let n = s.len();
        let mut count = [0; 3];
        let mut left = 0;
        let mut ans = 0;

        // 滑动窗口：right 作为子串右边界
        for right in 0..n {
            count[(s[right] - b'a') as usize] += 1;

            // 当窗口内包含 a,b,c 时，移动左指针缩小窗口
            while count[0] > 0 && count[1] > 0 && count[2] > 0 {
                // 以当前 left 为起点，right 为终点，所有以 right 为结尾的更长后缀都满足
                ans += n - right;

                // 移除左边界字符，尝试缩小窗口
                count[(s[left] - b'a') as usize] -= 1;
                left += 1;
            }
        }

        ans as i32
    }
}
```

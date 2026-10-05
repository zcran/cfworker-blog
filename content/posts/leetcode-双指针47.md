---
title: "leetcode-双指针47"
date: 2026-09-05T09:53:03+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 字符的最短距离

给你一个字符串 s 和一个字符 c ，且 c 是 s 中出现过的字符。

返回一个整数数组 answer ，其中 answer.length == s.length 且 answer[i] 是 s 中从下标 i 到离它 最近 的字符 c 的 距离 。

两个下标 i 和 j 之间的 距离 为 abs(i - j) ，其中 abs 是绝对值函数。


```
impl Solution {
    pub fn shortest_to_char(s: String, c: char) -> Vec<i32> {
        let bytes = s.as_bytes();
        let n = bytes.len();
        let c = c as u8;
        let mut ans = vec![0; n];

        // 从左向右遍历，记录到左侧最近 c 的距离
        let mut dist = n as i32; // 初始化为较大值
        for i in 0..n {
            if bytes[i] == c {
                dist = 0;
            } else if dist < n as i32 {
                dist += 1;
            }
            ans[i] = dist;
        }

        // 从右向左遍历，取到右侧最近 c 距离的最小值
        dist = n as i32;
        for i in (0..n).rev() {
            if bytes[i] == c {
                dist = 0;
            } else if dist < n as i32 {
                dist += 1;
            }
            ans[i] = ans[i].min(dist);
        }

        ans
    }
}
```

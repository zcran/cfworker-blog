---
title: "leetcode-模拟109"
date: 2026-08-08T11:31:15+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 候诊室中的最少椅子数

给你一个字符串 s，模拟每秒钟的事件 i：

如果 s[i] == 'E'，表示有一位顾客进入候诊室并占用一把椅子。
如果 s[i] == 'L'，表示有一位顾客离开候诊室，从而释放一把椅子。

返回保证每位进入候诊室的顾客都能有椅子坐的 最少 椅子数，假设候诊室最初是 空的 。


```
impl Solution {
    pub fn minimum_chairs(s: String) -> i32 {
        let mut chairs = 0;
        let mut max_chairs = 0;
        for c in s.bytes() {
            chairs += if c == b'E' { 1 } else { -1 };
            max_chairs = max_chairs.max(chairs);
        }
        max_chairs
    }
}
```

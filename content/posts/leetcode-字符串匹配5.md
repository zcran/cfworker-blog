---
title: "leetcode-字符串匹配5"
date: 2026-04-20T20:32:10+08:00
tags: ["leetcode", "字符串匹配"]
draft: false
---


## 旋转字符串

给定两个字符串, s 和 goal。如果在若干次旋转操作之后，s 能变成 goal ，那么返回 true 。

s 的 旋转操作 就是将 s 最左边的字符移动到最右边。

例如, 若 s = 'abcde'，在旋转一次之后结果就是'bcdea' 。


```
impl Solution {
    pub fn rotate_string(s: String, goal: String) -> bool {
        s.len() == goal.len() && goal.repeat(2).contains(&s)
    }
}
```


s.len() == goal.len()
        旋转不改变字符串长度。若长度不同，直接返回 false。

goal.repeat(2)
        将 goal 复制一份接到自身末尾，得到 goalgoal。例如 "abc" → "abcabc"。

.contains(&s)
        检查 goalgoal 是否包含子串 s。

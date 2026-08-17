---
title: "leetcode-模拟52"
date: 2026-08-08T11:31:11+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 执行操作后的变量值

存在一种仅支持 4 种操作和 1 个变量 X 的编程语言：

++X 和 X++ 使变量 X 的值 加 1
--X 和 X-- 使变量 X 的值 减 1

最初，X 的值是 0

给你一个字符串数组 operations ，这是由操作组成的一个列表，返回执行所有操作后， X 的 最终值 。


```
impl Solution {
    pub fn final_value_after_operations(operations: Vec<String>) -> i32 {
        // 核心洞察：所有 ++ 操作（++X / X++）的第 2 个字符都是 '+'，
        // 所有 -- 操作（--X / X--）的第 2 个字符都是 '-'。
        // 因此只需检查 s[1] 即可区分加减，无需完整匹配字符串。
        operations
            .iter()
            .map(|s| if s.as_bytes()[1] == b'+' { 1 } else { -1 })
            .sum()
    }
}
```

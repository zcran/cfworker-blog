---
title: "leetcode-栈33"
date: 2026-07-24T10:16:59+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 用栈操作构建数组

给你一个数组 target 和一个整数 n。

给你一个空栈和两种操作：

· "Push"：将一个整数加到栈顶。
· "Pop"：从栈顶删除一个整数。

同时给定一个范围 [1, n] 中的整数流。

使用两个栈操作使栈中的数字（从底部到顶部）等于 target。你应该遵循以下规则：

· 如果整数流不为空，从流中选取下一个整数并将其推送到栈顶。
· 如果栈不为空，弹出栈顶的整数。
· 如果，在任何时刻，栈中的元素（从底部到顶部）等于 target，则不要从流中读取新的整数，也不要对栈进行更多操作。

请返回遵循上述规则构建 target 所用的操作序列。如果存在多个合法答案，返回 任一 即可。


```
impl Solution {
    pub fn build_array(target: Vec<i32>, n: i32) -> Vec<String> {
        let mut ans = Vec::with_capacity(target.len() * 2);
        let mut target_idx = 0;

        // 只需遍历到 target 的最大值，之后的数字不需要处理
        for num in 1..=target[target.len() - 1] {
            ans.push("Push".to_string());

            if num == target[target_idx] {
                target_idx += 1;
            } else {
                ans.push("Pop".to_string());
            }
        }

        ans
    }
}
```

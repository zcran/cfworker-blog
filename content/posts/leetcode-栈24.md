---
title: "leetcode-栈24"
date: 2026-07-24T10:16:58+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 验证栈序列

给定 pushed 和 popped 两个序列，每个序列中的 值都不重复，只有当它们可能是在最初空栈上进行的推入 push 和弹出 pop 操作序列的结果时，返回 true；否则，返回 false 。



```
// 验证栈序列
// 模拟入栈出栈过程：入栈后立即尝试匹配出栈序列
impl Solution {
    pub fn validate_stack_sequences(pushed: Vec<i32>, popped: Vec<i32>) -> bool {
        let mut stack = Vec::with_capacity(pushed.len());
        let mut pop_index = 0;

        for &value in &pushed {
            stack.push(value);

            // 栈顶与出栈序列当前位置匹配时，持续弹出
            while let Some(&top) = stack.last() {
                if top == popped[pop_index] {
                    stack.pop();
                    pop_index += 1;
                } else {
                    break;
                }
            }
        }

        stack.is_empty()
    }
}
```

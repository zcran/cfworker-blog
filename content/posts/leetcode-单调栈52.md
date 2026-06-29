---
title: "leetcode-单调栈52"
date: 2026-06-07T10:14:06+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 栈排序

栈排序。 编写程序，对栈进行排序使最小元素位于栈顶。最多只能使用一个其他的临时栈存放数据，但不得将元素复制到别的数据结构（如数组）中。该栈支持如下操作：push、pop、peek 和 isEmpty。当栈为空时，peek 返回 -1。

```
/// 排序栈 - 维护一个单调递减栈（从栈底到栈顶严格递减）
///
/// 特性：
/// - push 操作保证栈中元素从栈底到栈顶单调递减
/// - pop 弹出栈顶（最小元素）
/// - peek 查看栈顶（最小元素）
///
/// 时间复杂度：push O(n)，pop O(1)，peek O(1)
/// 空间复杂度：O(n)
struct SortedStack {
    /// 存储元素，栈底在前，栈顶在后，保持单调递减
    stack: Vec<i32>,
}

impl SortedStack {
    /// 创建一个空的排序栈
    fn new() -> Self {
        SortedStack {
            stack: Vec::new(),
        }
    }

    /// 压入元素，保持栈的单调递减性质
    ///
    /// 算法：使用临时栈暂存比 val 小的元素，
    /// 压入 val 后再将临时栈的元素压回
    fn push(&mut self, val: i32) {
        let mut temp = Vec::new();

        // 弹出所有比 val 小的元素到临时栈
        while let Some(&top) = self.stack.last() {
            if top < val {
                temp.push(self.stack.pop().unwrap());
            } else {
                break;
            }
        }

        // 压入新元素
        self.stack.push(val);

        // 将临时栈的元素压回（保持原有顺序）
        while let Some(tmp_val) = temp.pop() {
            self.stack.push(tmp_val);
        }
    }

    /// 弹出栈顶元素（最小值）
    fn pop(&mut self) {
        self.stack.pop();
    }

    /// 查看栈顶元素（最小值），栈空时返回 -1
    fn peek(&self) -> i32 {
        self.stack.last().copied().unwrap_or(-1)
    }

    /// 判断栈是否为空
    fn is_empty(&self) -> bool {
        self.stack.is_empty()
    }
}
```

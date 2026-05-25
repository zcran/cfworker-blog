---
title: "leetcode-队列2"
date: 2026-05-23T09:21:37+08:00
tags: ["leetcode", "队列"]
draft: false
---


## 用栈实现队列

请你仅使用两个栈实现先入先出队列。队列应当支持一般队列支持的所有操作（push、pop、peek、empty）：

实现 MyQueue 类：

void push(int x) 将元素 x 推到队列的末尾
int pop() 从队列的开头移除并返回元素
int peek() 返回队列开头的元素
boolean empty() 如果队列为空，返回 true ；否则，返回 false

说明：

你 只能 使用标准的栈操作 —— 也就是只有 push to top, peek/pop from top, size, 和 is empty 操作是合法的。
你所使用的语言也许不支持栈。你可以使用 list 或者 deque（双端队列）来模拟一个栈，只要是标准的栈操作即可。


```
pub struct MyQueue {
    in_stack: Vec<i32>,  // 负责接收新元素（队尾）
    out_stack: Vec<i32>, // 负责弹出元素（队首）
}

impl MyQueue {
    /** 初始化空队列 */
    pub fn new() -> Self {
        Self {
            in_stack: Vec::new(),
            out_stack: Vec::new(),
        }
    }

    /** 将元素 x 加入队尾 */
    pub fn push(&mut self, x: i32) {
        self.in_stack.push(x);
    }

    /**
     * 确保 out_stack 中包含下一个待弹出的元素。
     * 若 out_stack 为空，则将 in_stack 中所有元素反转后移入 out_stack。
     */
    fn ensure_out(&mut self) {
        if self.out_stack.is_empty() {
            while let Some(v) = self.in_stack.pop() {
                self.out_stack.push(v);
            }
        }
    }

    /** 弹出队首元素并返回 */
    pub fn pop(&mut self) -> i32 {
        self.ensure_out();
        self.out_stack.pop().expect("queue is empty")
    }

    /** 获取队首元素（不弹出） */
    pub fn peek(&mut self) -> i32 {
        self.ensure_out();
        *self.out_stack.last().expect("queue is empty")
    }

    /** 判断队列是否为空 */
    pub fn empty(&self) -> bool {
        self.in_stack.is_empty() && self.out_stack.is_empty()
    }
}
```

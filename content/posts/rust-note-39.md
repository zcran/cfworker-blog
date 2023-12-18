---
title: "Rust Note 39"
date: 2023-06-10T19:51:15+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 最小栈

设计一个支持 push ，pop ，top 操作，并能在常数时间内检索到最小元素的栈。

实现 MinStack 类:

MinStack() 初始化堆栈对象。
void push(int val) 将元素val推入堆栈。
void pop() 删除堆栈顶部的元素。
int top() 获取堆栈顶部的元素。
int getMin() 获取堆栈中的最小元素。

### C 解法：
```
 // 辅助栈
 class MinStack {
    stack<int> x_stack;
    stack<int> min_stack;
public:
    MinStack() {
        min_stack.push(INT_MAX);
    }
    
    void push(int x) {
        x_stack.push(x);
        min_stack.push(min(min_stack.top(), x));
    }
    
    void pop() {
        x_stack.pop();
        min_stack.pop();
    }
    
    int top() {
        return x_stack.top();
    }
    
    int getMin() {
        return min_stack.top();
    }
};
```

### python解法：
```
 // 原地，栈里保存差值
 class MinStack:
    def __init__(self):
        """
        initialize your data structure here.
        """
        self.stack = []
        self.min_value = -1

    def push(self, x: int) -> None:
        if not self.stack:
            self.stack.append(0)
            self.min_value = x
        else:
            diff = x-self.min_value
            self.stack.append(diff)
            self.min_value = self.min_value if diff > 0 else x

    def pop(self) -> None:
        if self.stack:
            diff = self.stack.pop()
            if diff < 0:
                top = self.min_value
                self.min_value = top - diff
            else:
                top = self.min_value + diff
            return top

    def top(self) -> int:
        return self.min_value if self.stack[-1] < 0 else self.stack[-1] + self.min_value

    def getMin(self) -> int:
        return self.min_value if self.stack else -1
```

### Rust 解法：
```
use std::collections::VecDeque;

struct MinStack {
    q: VecDeque<i32>,
    min: i32,
}


/**
 * `&self` means the method takes an immutable reference.
 * If you need a mutable reference, change it to `&mut self` instead.
 */
impl MinStack {

    fn new() -> Self {
        Self {
            q: VecDeque::new(),
            min: 0,
        }
    }
    
    fn push(&mut self, val: i32) {
        self.min = if self.q.len() == 0 { val } else { self.min.min(val) };
        self.q.push_back(val);
    }
    
    fn pop(&mut self) {
        let res = self.q.pop_back().unwrap();
        if res == self.min {
            self.min = match self.q.iter().min() {
                Some(min) => *min,
                None => 0,
            };
        }
    }
    
    fn top(&self) -> i32 {
        *self.q.back().unwrap()
    }
    
    fn get_min(&self) -> i32 {
        self.min
    }
}

/**
 * Your MinStack object will be instantiated and called as such:
 * let obj = MinStack::new();
 * obj.push(val);
 * obj.pop();
 * let ret_3: i32 = obj.top();
 * let ret_4: i32 = obj.get_min();
 */
```
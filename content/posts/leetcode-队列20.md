---
title: "leetcode-队列20"
date: 2026-05-23T09:21:38+08:00
tags: ["leetcode", "队列"]
draft: false
---


## 图书整理 II


读者来到图书馆排队借还书，图书管理员使用两个书车来完成整理借还书的任务。书车中的书从下往上叠加存放，图书管理员每次只能拿取书车顶部的书。排队的读者会有两种操作：

push(bookID)：把借阅的书籍还到图书馆。
pop()：从图书馆中借出书籍。

为了保持图书的顺序，图书管理员每次取出供读者借阅的书籍是 最早 归还到图书馆的书籍。你需要返回 每次读者借出书的值 。

如果没有归还的书可以取出，返回 -1 。


```
#[derive(Default)]
struct CQueue {
    in_stack: Vec<i32>,   // 用于入队（append_tail）
    out_stack: Vec<i32>,  // 用于出队（delete_head）
}

impl CQueue {
    fn new() -> Self {
        Default::default()
    }

    /// 将元素添加到队列尾部
    fn append_tail(&mut self, value: i32) {
        self.in_stack.push(value);
    }

    /// 删除并返回队列头部元素，若队列为空则返回 -1
    fn delete_head(&mut self) -> i32 {
        // 如果出队栈为空，则将入队栈中的元素全部转移过来（顺序反转）
        if self.out_stack.is_empty() {
            while let Some(v) = self.in_stack.pop() {
                self.out_stack.push(v);
            }
        }
        // 从出队栈弹出元素，若仍为空则返回 -1
        self.out_stack.pop().unwrap_or(-1)
    }
}
```

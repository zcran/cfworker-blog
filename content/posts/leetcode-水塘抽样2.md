---
title: "Leetcode 水塘抽样2"
date: 2023-12-19T15:21:14+08:00
tags: ["leetcode", "水塘抽样"]
draft: false
---

## 链表随机节点

给你一个单链表，随机选择链表的一个节点，并返回相应的节点值。每个节点 被选中的概率一样 。

实现 Solution 类：

Solution(ListNode head) 使用整数数组初始化对象。
int getRandom() 从链表中随机选择一个节点并返回该节点的值。链表中所有节点被选中的概率相等。

```
struct Solution {
    list: Vec<i32>,
}

impl Solution {
    fn new(mut head: Option<Box<ListNode>>) -> Self {
        let mut list = Vec::new();
        while let Some(mut node) = head {
            list.push(node.val);
            head = node.next.take();
        }
        Self { list }
    }

    fn get_random(&self) -> i32 {
        use rand::{Rng, thread_rng};
        self.list[thread_rng().gen_range(0, self.list.len())]
    }
}
```
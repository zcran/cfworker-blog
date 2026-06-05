---
title: "leetcode-递归39"
date: 2026-05-26T10:00:18+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 图书整理 I


书店店员有一张链表形式的书单，每个节点代表一本书，节点中的值表示书的编号。为更方便整理书架，店员需要将书单倒过来排列，就可以从最后一本书开始整理，逐一将书放回到书架上。请倒序返回这个书单链表。


```
impl Solution {
    pub fn reverse_book_list(head: Option<Box<ListNode>>) -> Vec<i32> {
        let mut result = Vec::new();
        let mut current = head;

        // 遍历链表，收集所有节点的值
        while let Some(node) = current {
            result.push(node.val);
            current = node.next;
        }

        // 反转数组，得到倒序
        result.reverse();
        result
    }
}
```

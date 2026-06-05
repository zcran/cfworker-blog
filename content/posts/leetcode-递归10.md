---
title: "leetcode-递归10"
date: 2026-05-26T10:00:17+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 反转链表


给你单链表的头节点 head ，请你反转链表，并返回反转后的链表。

```
impl Solution {
    pub fn reverse_list(mut head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        if head==None{
            return None
        }
        //转换
        let mut vec=Vec::new();
        while let Some(node_b) = head{
            let node=*node_b;
            vec.push(node.val);
            head=node.next;
        }
        //反转并转换
        let mut lists=ListNode::new(vec[0]);
        let mut i=1;
        while let Some(v) = vec.get(i){
            lists=ListNode{next:Some(Box::new(lists)), val: *v};
            i+=1;
        }
        Some(Box::new(lists))
    }
}
```

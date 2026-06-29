---
title: "leetcode-单调栈19"
date: 2026-06-07T10:14:04+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 链表中的下一个更大节点

给定一个长度为 n 的链表 head

对于列表中的每个节点，查找下一个 更大节点 的值。也就是说，对于每个节点，找到它旁边的第一个节点的值，这个节点的值 严格大于 它的值。

返回一个整数数组 answer ，其中 answer[i] 是第 i 个节点( 从1开始 )的下一个更大的节点的值。如果第 i 个节点没有下一个更大的节点，设置 answer[i] = 0 。

```
impl Solution {
    pub fn next_larger_nodes(mut head: Option<Box<ListNode>>) -> Vec<i32> {
        use std::collections::VecDeque;          // 引入双端队列，用于存储索引（单调栈）

        let (mut node, mut queue) = (vec![], VecDeque::new());
        // node: 存储链表所有节点的值
        // queue: 存储尚未找到下一个更大值的元素索引（单调递减栈）

        // 第一遍遍历：将链表所有值按顺序存入 node 数组
        while head.is_some() {
            node.push(head.as_ref().unwrap().val); // 取出当前节点的值
            head = head.unwrap().next.take();      // 移动到下一个节点
        }

        let mut ret = vec![0; node.len()];         // 初始化结果数组，默认 0

        // 第二遍遍历：使用单调栈找到每个元素的下一个更大元素
        for i in 0..node.len() {
            // 当栈非空且栈顶索引对应的值小于当前节点值：
            // 说明栈顶索引处找到了下一个更大元素（即当前 node[i]）
            while !queue.is_empty() && node[queue[queue.len() - 1] as usize] < node[i] {
                ret[queue.pop_back().unwrap() as usize] = node[i];
                // 出栈并设置该位置的结果为当前节点值
            }
            // 将当前索引入栈（单调递减栈）
            queue.push_back(i as i32);
        }

        ret  // 返回结果数组
    }
}
```

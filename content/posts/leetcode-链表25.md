---
title: "leetcode-链表25"
date: 2026-06-29T10:55:35+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 从链表中删去总和值为零的连续节点

给你一个链表的头节点 head，请你编写代码，反复删去链表中由 总和 值为 0 的连续节点组成的序列，直到不存在这样的序列为止。

删除完毕后，请你返回最终结果链表的头节点。



你可以返回任何满足题目要求的答案。

（注意，下面示例中的所有序列，都是对 ListNode 对象序列化的表示。）

示例 1：

输入：head = [1,2,-3,3,1]
输出：[3,1]
提示：答案 [1,2,1] 也是正确的。
示例 2：

输入：head = [1,2,3,-3,4]
输出：[1,2,4]
示例 3：

输入：head = [1,2,3,-3,-2]
输出：[1]


```
// Definition for singly-linked list.
// #[derive(PartialEq, Eq, Clone, Debug)]
// pub struct ListNode {
//   pub val: i32,
//   pub next: Option<Box<ListNode>>
// }
//
// impl ListNode {
//   #[inline]
//   fn new(val: i32) -> Self {
//     ListNode {
//       next: None,
//       val
//     }
//   }
// }

impl Solution {
    /// 删除链表中所有和为 0 的连续子序列
    ///
    /// 算法步骤：
    /// 1. 将链表转换为 Vec<i32>，方便操作
    /// 2. 反复扫描，找到第一个和为 0 的连续子序列并删除，直到没有为止
    /// 3. 根据剩余的数值重建链表
    ///
    /// 时间复杂度：最坏 O(n²)，但链表长度有限时性能可接受
    /// 空间复杂度：O(n)，存储数值的 Vec
    pub fn remove_zero_sum_sublists(head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        // 将链表值提取到 Vec 中
        let mut vals = Vec::new();
        let mut cur = head;
        while let Some(node) = cur {
            vals.push(node.val);
            cur = node.next;
        }

        // 重复删除和为 0 的子序列，直到没有变化
        let mut has_deleted = true;
        while has_deleted {
            has_deleted = false;
            let mut start = 0;
            while start < vals.len() {
                let mut sum = 0;
                let mut end = start;
                // 从 start 开始寻找第一个和为 0 的连续段
                while end < vals.len() {
                    sum += vals[end];
                    if sum == 0 {
                        // 删除 [start, end] 区间
                        vals.drain(start..=end);
                        has_deleted = true;
                        break;
                    }
                    end += 1;
                }
                if has_deleted {
                    break; // 删除后从头开始扫描
                }
                start += 1;
            }
        }

        // 重建链表（从后往前构建）
        let mut head = None;
        for &val in vals.iter().rev() {
            head = Some(Box::new(ListNode {
                val,
                next: head,
            }));
        }
        head
    }
}
```

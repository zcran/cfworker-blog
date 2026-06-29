---
title: "leetcode-数论38"
date: 2026-06-20T11:09:56+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 在链表中插入最大公约数

给你一个链表的头 head ，每个结点包含一个整数值。

在相邻结点之间，请你插入一个新的结点，结点值为这两个相邻结点值的 最大公约数 。

请你返回插入之后的链表。

两个数的 最大公约数 是可以被两个数字整除的最大正整数。


```
impl Solution {
    /// 在相邻节点之间插入它们的最大公约数
    ///
    /// 遍历链表，每两个相邻节点间插入一个新节点，值为两节点值的 gcd
    pub fn insert_greatest_common_divisors(
        mut head: Option<Box<ListNode>>
    ) -> Option<Box<ListNode>> {
        let mut cur = head.as_mut();

        while let Some(node) = cur {
            if let Some(next) = node.next.as_mut() {
                // 在 node 和 next 之间插入新节点
                let gcd_val = gcd(node.val, next.val);
                let new_node = Box::new(ListNode {
                    val: gcd_val,
                    next: node.next.take(),
                });
                node.next = Some(new_node);

                // 移动到插入节点后的下一个节点（跳过刚插入的节点）
                cur = node.next.as_mut().and_then(|n| n.next.as_mut());
            } else {
                break;
            }
        }

        head
    }
}

/// 计算两个数的最大公约数（欧几里得算法）
#[inline]
fn gcd(mut a: i32, mut b: i32) -> i32 {
    while b != 0 {
        let temp = b;
        b = a % b;
        a = temp;
    }
    a.abs()
}
```

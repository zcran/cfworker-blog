---
title: "leetcode-双指针19"
date: 2026-09-05T09:53:01+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 寻找重复数

给定一个包含 n + 1 个整数的数组 nums ，其数字都在 [1, n] 范围内（包括 1 和 n），可知至少存在一个重复的整数。

假设 nums 只有 一个重复的整数 ，返回 这个重复的数 。

你设计的解决方案必须 不修改 数组 nums 且只用常量级 O(1) 的额外空间。


```
impl Solution {
    /// 寻找重复数（弗洛伊德判圈算法）
    /// 时间复杂度 O(n)，空间复杂度 O(1)
    /// 将数组视为链表，值作为下一个节点的索引，重复数即为环的入口
    pub fn find_duplicate(nums: Vec<i32>) -> i32 {
        // 阶段1：检测环（快慢指针相遇）
        let mut slow = 0;
        let mut fast = 0;
        loop {
            slow = nums[slow as usize];
            fast = nums[nums[fast as usize] as usize];
            if slow == fast {
                break;
            }
        }

        // 阶段2：寻找环的入口（从起点和相遇点同时出发）
        let mut head = 0;
        while slow != head {
            slow = nums[slow as usize];
            head = nums[head as usize];
        }

        slow
    }
}
```

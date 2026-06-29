---
title: "leetcode-单调栈31"
date: 2026-06-07T10:14:05+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 队列中可以看到的人数

有 n 个人排成一个队列，从左到右 编号为 0 到 n - 1 。给你以一个整数数组 heights ，每个整数 互不相同，heights[i] 表示第 i 个人的高度。

一个人能 看到 他右边另一个人的条件是这两人之间的所有人都比他们两人 矮 。更正式的，第 i 个人能看到第 j 个人的条件是 i < j 且 min(heights[i], heights[j]) > max(heights[i+1], heights[i+2], ..., heights[j-1]) 。

请你返回一个长度为 n 的数组 answer ，其中 answer[i] 是第 i 个人在他右侧队列中能 看到 的 人数 。


```
impl Solution {
    /// 计算每个人能看到的人数（站在一排人里，向右看，矮的人会被高的人挡住）
    ///
    /// 规则：对于位置 i，看右边所有 j>i，如果区间 (i,j) 内所有人的身高都小于 min(heights[i], heights[j])，
    /// 则 i 能看到 j。等价于：向右找递增序列（严格递增才能被看到）
    ///
    /// 方法：从右向左遍历 + 单调递减栈
    ///
    /// 时间复杂度 O(n)  空间复杂度 O(n)
    pub fn can_see_persons_count(heights: Vec<i32>) -> Vec<i32> {
        let n = heights.len();
        let mut ans = vec![0; n];      // 存储每个人能看到的人数
        let mut stack = Vec::new();     // 单调递减栈（从栈底到栈顶严格递减）

        // 从右向左遍历，保证栈中元素都是当前右边的人
        for (i, &h) in heights.iter().enumerate().rev() {
            // 弹出所有比当前人矮的栈顶元素（这些人都能被当前人看到）
            // 因为当前人比它们高，且中间没有更高的人（栈维护了递增的观察链）
            while !stack.is_empty() && *stack.last().unwrap() < h {
                stack.pop();
                ans[i] += 1;           // 每弹出一个，说明当前人能直接看到这个人
            }

            // 如果栈非空，说明栈顶的人比当前人高，当前人还能看到这个人（第一个比它高的人）
            if !stack.is_empty() {
                ans[i] += 1;
            }

            // 当前人入栈，作为左边人的观察对象
            stack.push(h);
        }

        ans
    }
}
```

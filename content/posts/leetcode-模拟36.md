---
title: "leetcode-模拟36"
date: 2026-08-08T11:31:10+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 查找大小为 M 的最新分组

给你一个数组 arr ，该数组表示一个从 1 到 n 的数字排列。有一个长度为 n 的二进制字符串，该字符串上的所有位最初都设置为 0 。

在从 1 到 n 的每个步骤 i 中（假设二进制字符串和 arr 都是从 1 开始索引的情况下），二进制字符串上位于位置 arr[i] 的位将会设为 1 。

给你一个整数 m ，请你找出二进制字符串上存在长度为 m 的一组 1 的最后步骤。一组 1 是一个连续的、由 1 组成的子串，且左右两边不再有可以延伸的 1 。

返回存在长度 恰好 为 m 的 一组 1  的最后步骤。如果不存在这样的步骤，请返回 -1 。


```
impl Solution {
    pub fn find_latest_step(arr: Vec<i32>, m: i32) -> i32 {
        let n = arr.len();
        let m = m as usize;

        // 并查集思想：记录每个位置所在连续 1 区间的左右端点
        // 仅在被置为 1 的位置（即区间的端点）存储区间范围
        let mut bound = vec![(0, 0); n + 2];
        let mut ans = -1;
        let mut cnt = 0; // 当前长度恰好为 m 的区间个数

        for (step, &v) in arr.iter().enumerate() {
            let i = v as usize;
            let (mut left, mut right) = (i, i);

            // 向左合并：若左邻位为 1，则取其区间的左端点
            if bound[i - 1].0 != 0 {
                left = bound[i - 1].0;
                if bound[i - 1].1 - left + 1 == m {
                    cnt -= 1; // 原左区间长度恰好为 m，合并后消失
                }
            }
            // 向右合并：若右邻位为 1，则取其区间的右端点
            if bound[i + 1].1 != 0 {
                right = bound[i + 1].1;
                if right - bound[i + 1].0 + 1 == m {
                    cnt -= 1; // 原右区间长度恰好为 m，合并后消失
                }
            }

            // 新合并后的区间长度恰好为 m
            if right - left + 1 == m {
                cnt += 1;
            }

            // 只要当前存在长度恰好为 m 的区间，更新答案
            if cnt > 0 {
                ans = step as i32 + 1;
            }

            // 在新区间的两个端点记录完整区间范围
            bound[left] = (left, right);
            bound[right] = (left, right);
        }

        ans
    }
}
```

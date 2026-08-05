---
title: "leetcode-滑动窗口44"
date: 2026-07-18T11:02:31+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 跳跃游戏 VII

给你一个下标从 0 开始的二进制字符串 s 和两个整数 minJump 和 maxJump 。一开始，你在下标 0 处，且该位置的值一定为 '0' 。当同时满足如下条件时，你可以从下标 i 移动到下标 j 处：

i + minJump <= j <= min(i + maxJump, s.length - 1) 且
s[j] == '0'.

如果你可以到达 s 的下标 s.length - 1 处，请你返回 true ，否则返回 false 。


```
impl Solution {
    pub fn can_reach(s: String, min_jump: i32, max_jump: i32) -> bool {
        let n = s.len();
        let min_j = min_jump as usize;
        let max_j = max_jump as usize;

        // 如果起点或终点不可达，直接返回
        if s.as_bytes()[0] != b'0' || s.as_bytes()[n - 1] != b'0' {
            return false;
        }

        let bytes = s.as_bytes();
        let mut reachable = vec![false; n];
        reachable[0] = true;

        // prefix[i] 表示 reachable[0..=i] 中 true 的数量
        let mut prefix = vec![0; n];
        prefix[0] = 1;

        // 初始化前缀和，使得后续区间查询可以正确处理
        for i in 1..min_j {
            prefix[i] = prefix[i - 1];
        }

        // 从 min_j 开始遍历，因为 i < min_j 不可能从任何位置跳转到达
        for i in min_j..n {
            if bytes[i] == b'0' {
                // 可跳转区间 [i - max_j, i - min_j]
                let left = if i >= max_j { i - max_j } else { 0 };
                let right = i - min_j;

                // 区间内是否有可达点
                let count = if left == 0 {
                    prefix[right]
                } else {
                    prefix[right] - prefix[left - 1]
                };

                if count > 0 {
                    reachable[i] = true;
                }
            }

            // 更新前缀和
            prefix[i] = prefix[i - 1] + if reachable[i] { 1 } else { 0 };
        }

        reachable[n - 1]
    }
}
```

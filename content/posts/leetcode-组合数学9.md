---
title: "leetcode-组合数学9"
date: 2026-05-24T09:54:12+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 第 K 条最小指令


Bob 站在单元格 (0, 0) ，想要前往目的地 destination ：(row, column) 。他只能向 右 或向 下 走。你可以为 Bob 提供导航 指令 来帮助他到达目的地 destination 。

指令 用字符串表示，其中每个字符：

'H' ，意味着水平向右移动
'V' ，意味着竖直向下移动
能够为 Bob 导航到目的地 destination 的指令可以有多种，例如，如果目的地 destination 是 (2, 3)，"HHHVV" 和 "HVHVH" 都是有效 指令 。

然而，Bob 很挑剔。因为他的幸运数字是 k，他想要遵循 按字典序排列后的第 k 条最小指令 的导航前往目的地 destination 。k  的编号 从 1 开始 。

给你一个整数数组 destination 和一个整数 k ，请你返回可以为 Bob 提供前往目的地 destination 导航的 按字典序排列后的第 k 条最小指令 。


```
impl Solution {
    pub fn kth_smallest_path(destination: Vec<i32>, k: i32) -> String {

        let n = destination[1] as usize;
        let x = destination[0] as usize;
        let mut dp = vec![vec![1; x + 1]; n + 1];
        for i in 1..=n {
            for j in 1..=x {
                dp[i][j] = dp[i][j - 1] + dp[i - 1][j];
            }
        }

        let sum = x + n;
        let mut s = String::new();
        let mut ix = n;
        let mut iy = x;
        let mut k = k;

        while ix >= 1 {
            if k <= dp[ix - 1][iy] {
                ix -= 1;
                s.push('H');
            } else {
                k -= dp[ix - 1][iy];
                iy -= 1;
                s.push('V');
            }
        }

        for _ in 0..(sum - s.len()) {
            s.push('V');
        }

        return s;
    }
}


```

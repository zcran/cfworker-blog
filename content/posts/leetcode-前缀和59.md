---
title: "leetcode-前缀和59"
date: 2026-08-25T10:02:49+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 用地毯覆盖后的最少白色砖块

给你一个下标从 0 开始的 二进制 字符串 floor ，它表示地板上砖块的颜色。

floor[i] = '0' 表示地板上第 i 块砖块的颜色是 黑色 。
floor[i] = '1' 表示地板上第 i 块砖块的颜色是 白色 。

同时给你 numCarpets 和 carpetLen 。你有 numCarpets 条 黑色 的地毯，每一条 黑色 的地毯长度都为 carpetLen 块砖块。请你使用这些地毯去覆盖砖块，使得未被覆盖的剩余 白色 砖块的数目 最小 。地毯相互之间可以覆盖。

请你返回没被覆盖的白色砖块的 最少 数目。


```
use std::cmp::{max, min};

impl Solution {
    pub fn minimum_white_tiles(floor: String, num_carpets: i32, carpet_len: i32) -> i32 {
        // 将输入参数转换为 usize 类型以便索引
        let num_carpets = num_carpets as usize;
        let carpet_len = carpet_len as usize;

        // 将字符串转换为字符数组方便遍历
        let floor: Vec<_> = floor.chars().collect();
        let n = floor.len();

        // dp[i][j] 表示：处理前 i 块砖块，使用 j 条地毯时，最少剩余的白色砖块数量
        // 初始化为一个较大的值（i32::MAX/2）避免溢出
        let mut dp = vec![vec![i32::MAX / 2; num_carpets + 1]; n + 1];

        // 边界条件1：没有砖块时（i=0），无论多少条地毯，白色砖块数都是 0
        for j in 0..=num_carpets {
            dp[0][j] = 0;
        }

        // 边界条件2：不使用地毯时（j=0），剩余白色砖块数就是前 i 块中白色砖块的总数
        for i in 1..=n {
            dp[i][0] = dp[i - 1][0] + if floor[i - 1] == '1' { 1 } else { 0 };
        }

        // 动态规划状态转移
        for i in 1..=n {
            for j in 1..=num_carpets {
                // 情况1：不覆盖第 i 块砖
                // 如果第 i 块是白色，剩余白色砖块数 +1
                dp[i][j] = dp[i - 1][j] + if floor[i - 1] == '1' { 1 } else { 0 };

                // 情况2：用一条地毯覆盖最后 carpet_len 块砖（从 i-carpet_len+1 到 i）
                // 如果 i < carpet_len，则覆盖从第 1 块到第 i 块
                let prev_index = if i >= carpet_len { i - carpet_len } else { 0 };
                // 使用一条地毯后，消耗一条地毯（j-1），前 prev_index 块砖的最优解为 dp[prev_index][j-1]
                dp[i][j] = min(dp[i][j], dp[prev_index][j - 1]);
            }
        }

        // 返回处理所有砖块，使用 num_carpets 条地毯的最优解
        dp[n][num_carpets]
    }
}
```

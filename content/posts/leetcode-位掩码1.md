---
title: "leetcode-位掩码1"
date: 2026-05-18T10:27:58+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 火柴拼正方形

你将得到一个整数数组 matchsticks ，其中 matchsticks[i] 是第 i 个火柴棒的长度。你要用 所有的火柴棍 拼成一个正方形。你 不能折断 任何一根火柴棒，但你可以把它们连在一起，而且每根火柴棒必须 使用一次 。

如果你能使这个正方形，则返回 true ，否则返回 false 。

```
impl Solution {
    pub fn makesquare(matchsticks: Vec<i32>) -> bool {
        // 总和必须能被4整除
        let total: i32 = matchsticks.iter().sum();
        if total % 4 != 0 {
            return false;
        }
        let target = total / 4;

        // 任何一根火柴超过边长则直接失败
        if matchsticks.iter().any(|&x| x > target) {
            return false;
        }

        // 降序排序，优先放置长火柴，加速剪枝
        let mut matchsticks = matchsticks;
        matchsticks.sort_by(|a, b| b.cmp(a));

        let mut sides = [0; 4]; // 四条边的当前长度，固定大小数组

        // DFS：尝试将第 index 根火柴放入某一边
        fn dfs(index: usize, target: i32, sides: &mut [i32; 4], sticks: &[i32]) -> bool {
            if index == sticks.len() {
                return true; // 所有火柴已放置完毕
            }
            let stick = sticks[index];
            for i in 0..4 {
                // 剪枝1：放不下或已满
                if sides[i] + stick > target {
                    continue;
                }
                // 剪枝2：如果当前边和前一条边长度相同，则跳过（避免对称重复）
                if i > 0 && sides[i] == sides[i - 1] {
                    continue;
                }
                sides[i] += stick;
                if dfs(index + 1, target, sides, sticks) {
                    return true;
                }
                sides[i] -= stick; // 回溯
            }
            false
        }

        dfs(0, target, &mut sides, &matchsticks)
    }
}
```

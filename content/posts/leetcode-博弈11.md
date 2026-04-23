---
title: "leetcode-博弈11"
date: 2026-04-09T20:51:31+08:00
tags: ["leetcode", "博弈"]
draft: false
---


## 石子游戏 VIII

Alice 和 Bob 玩一个游戏，两人轮流操作， Alice 先手 。

总共有 n 个石子排成一行。轮到某个玩家的回合时，如果石子的数目 大于 1 ，他将执行以下操作：

1.选择一个整数 x > 1 ，并且 移除 最左边的 x 个石子。
2.将 移除 的石子价值之 和 累加到该玩家的分数中。
3.将一个 新的石子 放在最左边，且新石子的值为被移除石子值之和。

当只剩下 一个 石子时，游戏结束。

Alice 和 Bob 的 分数之差 为 (Alice 的分数 - Bob 的分数) 。 Alice 的目标是 最大化 分数差，Bob 的目标是 最小化 分数差。

给你一个长度为 n 的整数数组 stones ，其中 stones[i] 是 从左边起 第 i 个石子的价值。请你返回在双方都采用 最优 策略的情况下，Alice 和 Bob 的 分数之差 。


```
// Stone Game VIII 的 Rust 解法
// 算法思路：
// 1. 计算前缀和 s[i] = stones[0] + ... + stones[i] (i 从 0 到 n-1)
// 2. 初始答案 ans = s[n-1] (即整个数组的和)
// 3. 从 i = n-2 向下遍历到 1，每一步更新 ans = max(s[i] - ans, ans)
// 4. 最终 ans 即为先手玩家能获得的最大分数差值（转化为整数返回）
impl Solution {
    pub fn stone_game_viii(stones: Vec<i32>) -> i32 {
        let n = stones.len();
        // 使用 i64 避免前缀和溢出（原题数据范围可能较大）
        // 函数式风格：用 scan 计算前缀和，并收集为 Vec<i64>
        let prefix_sums: Vec<i64> = stones
            .iter()
            .map(|&x| x as i64)           // 将 i32 转为 i64
            .scan(0, |acc, x| {
                *acc += x;
                Some(*acc)
            })
            .collect();

        // 边界情况：如果数组长度小于 2，根据题目规则直接返回总和（但原题保证 n >= 2）
        if n < 2 {
            return stones.iter().sum::<i32>();
        }

        // 初始化答案：整个数组的和（即 s[n-1]）
        let mut ans = *prefix_sums.last().unwrap();

        // 从倒数第二个前缀和开始逆向递推，直到索引 1（包含）
        // 使用函数式的 fold 风格，但保持可读性，这里用 for 循环更清晰
        for i in (1..=n - 2).rev() {
            // 状态转移：当前玩家可以选择从前面取一段，得到 s[i] - 对手最优，然后取最大值
            ans = ans.max(prefix_sums[i] - ans);
        }

        // 将 i64 结果转换为 i32（题目保证答案在 i32 范围内）
        ans as i32
    }
}
```

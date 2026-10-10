---
title: "leetcode-数组40"
date: 2026-10-10T20:38:43+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 有相同颜色的相邻元素数目

给定一个整数 n 表示一个长度为 n 的数组  colors，初始所有元素均为 0 ，表示是 未染色 的。同时给定一个二维整数数组 queries，其中 queries[i] = [indexi, colori]。对于第 i 个 查询：

将 colors[indexi] 染色为 colori。
统计 colors 中颜色相同的相邻对的数量（无论 colori）。

请你返回一个长度与 queries 相等的数组 answer ，其中 answer[i]是前 i 个操作的答案。


```
impl Solution {
    /// 模拟染色过程，每次染色后统计颜色相同的相邻对数量。
    ///
    /// 用 `cnt` 维护当前相邻同色对数。每次把 `colors[i]` 从旧色改为新色时：
    /// 1. 若旧色非 0，减去 `i` 与左右邻居原本的同色对；
    /// 2. 更新 `colors[i]`；
    /// 3. 若新色非 0，加上 `i` 与左右邻居现在的同色对。
    ///
    /// 时间复杂度 O(q)，空间复杂度 O(n)（颜色数组 + 结果数组）。
    pub fn color_the_array(n: i32, queries: Vec<Vec<i32>>) -> Vec<i32> {
        let n = n as usize;
        let mut colors = vec![0; n];
        let mut answer = Vec::with_capacity(queries.len());
        let mut cnt = 0;

        for query in queries {
            let i = query[0] as usize;
            let new_color = query[1];

            // 颜色未变，直接记录
            if colors[i] == new_color {
                answer.push(cnt);
                continue;
            }

            // 撤销旧色对相邻同色对的贡献
            if colors[i] != 0 {
                if i > 0 && colors[i - 1] == colors[i] {
                    cnt -= 1;
                }
                if i + 1 < n && colors[i + 1] == colors[i] {
                    cnt -= 1;
                }
            }

            colors[i] = new_color;

            // 加上新色对相邻同色对的贡献
            if i > 0 && colors[i - 1] == colors[i] {
                cnt += 1;
            }
            if i + 1 < n && colors[i + 1] == colors[i] {
                cnt += 1;
            }

            answer.push(cnt);
        }

        answer
    }
}
```

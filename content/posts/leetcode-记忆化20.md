---
title: "leetcode-记忆化20"
date: 2026-05-06T20:22:34+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 统计所有可行路径

给你一个 互不相同 的整数数组，其中 locations[i] 表示第 i 个城市的位置。同时给你 start，finish 和 fuel 分别表示出发城市、目的地城市和你初始拥有的汽油总量

每一步中，如果你在城市 i ，你可以选择任意一个城市 j ，满足  j != i 且 0 <= j < locations.length ，并移动到城市 j 。从城市 i 移动到 j 消耗的汽油量为 |locations[i] - locations[j]|，|x| 表示 x 的绝对值。

请注意， fuel 任何时刻都 不能 为负，且你 可以 经过任意城市超过一次（包括 start 和 finish ）。

请你返回从 start 到 finish 所有可能路径的数目。

由于答案可能很大， 请将它对 10^9 + 7 取余后返回。

```
impl Solution {
    /// 计算所有可能的路由数量。
    /// 给定城市位置数组 locations，从起点 start 出发，到达终点 finish，
    /// 初始油量为 fuel。每次移动消耗两个城市间的距离（绝对值），且油量必须非负。
    /// 返回从 start 到 finish 的不同路径总数（结果对 1e9+7 取模）。
    pub fn count_routes(locations: Vec<i32>, start: i32, finish: i32, fuel: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = locations.len();
        let start = start as usize;
        let finish = finish as usize;
        let fuel = fuel as usize;

        // 记忆化数组：f[pos][rest] 表示从位置 pos 出发，剩余油量 rest 时，能到达 finish 的路径数
        // 初始化为 -1 表示未计算。
        let mut memo = vec![vec![-1i64; fuel + 1]; n];

        // 递归函数，使用引用传递 memo 和 locations，同时捕获 finish, MOD
        fn dfs(
            pos: usize,
            rest: usize,
            memo: &mut Vec<Vec<i64>>,
            locations: &[i32],
            finish: usize,
            mod_val: i64,
        ) -> i64 {
            // 如果已经计算过，直接返回
            if memo[pos][rest] != -1 {
                return memo[pos][rest];
            }

            // 剪枝：即使直线前往 finish 也油量不足，则无路可走
            let dist_to_finish = (locations[pos] - locations[finish]).abs() as usize;
            if dist_to_finish > rest {
                memo[pos][rest] = 0;
                return 0;
            }

            let mut total = 0i64;

            // 尝试移动到所有其他城市（包括再次经过 finish，但排除了原地不动）
            for (i, &next_loc) in locations.iter().enumerate() {
                if i == pos {
                    continue;
                }
                let cost = (locations[pos] - next_loc).abs() as usize;
                if cost <= rest {
                    total += dfs(i, rest - cost, memo, locations, finish, mod_val);
                    total %= mod_val;
                }
            }

            // 如果当前城市已经是终点，则可以停止移动，这也算一条有效路径
            if pos == finish {
                total = (total + 1) % mod_val;
            }

            memo[pos][rest] = total;
            total
        }

        (dfs(start, fuel, &mut memo, &locations, finish, MOD) % MOD) as i32
    }
}
```

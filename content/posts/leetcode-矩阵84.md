---
title: "leetcode-矩阵84"
date: 2026-09-19T10:41:37+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 守卫城堡

城堡守卫游戏的胜利条件为使恶魔无法从出生点到达城堡。游戏地图可视作 2*N 的方格图，记作字符串数组 grid，其中：

"." 表示恶魔可随意通行的平地；
"#" 表示恶魔不可通过的障碍物，玩家可通过在 平地 上设置障碍物，即将 "." 变为 "#" 以阻挡恶魔前进；
"S" 表示恶魔出生点，将有大量的恶魔该点生成，恶魔可向上/向下/向左/向右移动，且无法移动至地图外；
"P" 表示瞬移点，移动到 "P" 点的恶魔可被传送至任意一个 "P" 点，也可选择不传送；
"C" 表示城堡。

然而在游戏中用于建造障碍物的金钱是有限的，请返回玩家最少需要放置几个障碍物才能获得胜利。若无论怎样放置障碍物均无法获胜，请返回 -1。

注意：

地图上可能有一个或多个出生点
地图上有且只有一个城堡


```
impl Solution {
    pub fn guard_castle(grid: Vec<String>) -> i32 {
        const INF: i32 = i32::MAX / 2;
        let n = grid[0].len();
        let mut ans = INF;

        // 情况 1：将所有传送点 P 视为城堡 C
        let mut g1: Vec<Vec<u8>> = grid.iter().map(|s| s.as_bytes().to_vec()).collect();
        for i in 0..2 {
            for j in 0..n {
                if g1[i][j] == b'P' {
                    g1[i][j] = b'C';
                }
            }
        }
        ans = ans.min(Self::check(&g1));

        // 情况 2：将所有传送点 P 视为恶魔出生点 S
        let mut g2: Vec<Vec<u8>> = grid.iter().map(|s| s.as_bytes().to_vec()).collect();
        for i in 0..2 {
            for j in 0..n {
                if g2[i][j] == b'P' {
                    g2[i][j] = b'S';
                }
            }
        }
        ans = ans.min(Self::check(&g2));

        if ans >= INF { -1 } else { ans }
    }

    // 对 2 x n 的网格进行轮廓线 DP，计算最少放置障碍物数
    fn check(grid: &Vec<Vec<u8>>) -> i32 {
        const INF: i32 = i32::MAX / 2;
        let n = grid[0].len();

        // 快速检查：原始网格中是否存在直接相邻的 C 和 S
        for i in 0..2 {
            for j in 0..n {
                if j + 1 < n {
                    let (a, b) = (grid[i][j], grid[i][j + 1]);
                    if (a == b'C' && b == b'S') || (a == b'S' && b == b'C') {
                        return INF;
                    }
                }
                if i + 1 < 2 {
                    let (a, b) = (grid[i][j], grid[i + 1][j]);
                    if (a == b'C' && b == b'S') || (a == b'S' && b == b'C') {
                        return INF;
                    }
                }
            }
        }

        // 状态编码：0=空, 1=城堡, 2=恶魔, 3=障碍
        let rep = |c: u8| -> usize {
            match c {
                b'.' => 0,
                b'C' => 1,
                b'S' => 2,
                _ => 3, // '#'
            }
        };

        // f[s1][s2]：上一列两行的状态（经过连通性传播后的类型）
        let mut f = vec![vec![INF; 4]; 4];
        f[0][0] = 0;

        for j in 0..n {
            let orig1 = rep(grid[0][j]);
            let orig2 = rep(grid[1][j]);

            let mut nf = vec![vec![INF; 4]; 4];

            for s1 in 0..4 {
                for s2 in 0..4 {
                    if f[s1][s2] >= INF {
                        continue;
                    }
                    let base = f[s1][s2];

                    // 枚举当前列的可能状态：不放障碍、上行放障碍、下行放障碍、两行都放障碍
                    let mut options = vec![(orig1, orig2, 0)];
                    if grid[0][j] == b'.' {
                        options.push((3, orig2, 1));
                    }
                    if grid[1][j] == b'.' {
                        options.push((orig1, 3, 1));
                    }
                    if grid[0][j] == b'.' && grid[1][j] == b'.' {
                        options.push((3, 3, 2));
                    }

                    for (mut t1, mut t2, extra) in options {
                        // 与上一列的垂直相邻检查与继承
                        if s1 == 1 || s1 == 2 {
                            if s1 + t1 == 3 { continue; } // C 与 S 相邻
                            if t1 == 0 { t1 = s1; }      // 空单元格继承上一列类型
                        }
                        if s2 == 1 || s2 == 2 {
                            if s2 + t2 == 3 { continue; }
                            if t2 == 0 { t2 = s2; }
                        }
                        // 当前列上下相邻检查与继承
                        if (t1 == 1 || t1 == 2) && (t1 + t2 == 3) { continue; }
                        if (t1 == 1 || t1 == 2) && t2 == 0 { t2 = t1; }
                        if (t2 == 1 || t2 == 2) && t1 == 0 { t1 = t2; }

                        let cost = base + extra;
                        if cost < nf[t1][t2] {
                            nf[t1][t2] = cost;
                        }
                    }
                }
            }
            f = nf;
        }

        // 最后一列任意状态均可，取最小值
        let mut res = INF;
        for s1 in 0..4 {
            for s2 in 0..4 {
                res = res.min(f[s1][s2]);
            }
        }
        res
    }
}
```

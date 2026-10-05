---
title: "leetcode-矩阵74"
date: 2026-09-19T10:41:37+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 交替方向的最小路径代价 II

给你两个整数 m 和 n，分别表示网格的行数和列数。

进入单元格 (i, j) 的成本定义为 (i + 1) * (j + 1)。

另外给你一个二维整数数组 waitCost，其中 waitCost[i][j] 定义了在该单元格 等待 的成本。

路径始终从第 1 步进入单元格 (0, 0) 并支付入场花费开始。

每一步，你都遵循交替模式：

在 奇数秒 ，你必须向 右 或向 下 移动到 相邻 的单元格，并支付其进入成本。
在 偶数秒 ，你必须原地 等待恰好 1 秒并在 1 秒期间支付 waitCost[i][j]。

返回到达 (m - 1, n - 1) 所需的 最小 总成本。


```
impl Solution {
    pub fn min_cost(m: i32, n: i32, wait_cost: Vec<Vec<i32>>) -> i64 {
        let m = m as usize;
        let n = n as usize;

        // f[j] 表示到达当前行第 j-1 列时的最小累计成本（滚动数组，多开一列方便处理）
        // 初始化为 i64::MAX 表示不可达
        let mut f = vec![i64::MAX; n + 1];

        // 起点特殊处理：进入 (0,0) 的成本为 1*1=1，但最后统一扣掉一次 waitCost[0][0]
        // 这里先设为 -waitCost[0][0]，加上后面的 (i+1)*(j+1) 后正好抵消多算的等待成本
        f[1] = -wait_cost[0][0] as i64;

        for i in 0..m {
            for j in 0..n {
                // 从左侧 f[j]（当前行前一列）或上方 f[j+1]（上一行同列）转移过来
                let best = f[j].min(f[j + 1]);
                // 加上进入成本 (i+1)*(j+1) 和等待成本 wait_cost[i][j]
                f[j + 1] = best + wait_cost[i][j] as i64 + ((i + 1) * (j + 1)) as i64;
            }
        }

        // 终点不需要再等待，扣除最后多加的一次 waitCost
        f[n] - wait_cost[m - 1][n - 1] as i64
    }
}
```

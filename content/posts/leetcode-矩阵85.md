---
title: "leetcode-矩阵85"
date: 2026-09-19T10:41:37+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 打地鼠

欢迎各位勇者来到力扣城，本次试炼主题为「打地鼠」。

勇者面前有一个大小为 3*3 的打地鼠游戏机，地鼠将随机出现在各个位置，moles[i] = [t,x,y] 表示在第 t 秒会有地鼠出现在 (x,y) 位置上，并于第 t+1 秒该地鼠消失。

勇者有一把可敲打地鼠的锤子，初始时刻（即第 0 秒）锤子位于正中间的格子 (1,1)，锤子的使用规则如下：

锤子每经过 1 秒可以往上、下、左、右中的一个方向移动一格，也可以不移动
锤子只可敲击所在格子的地鼠，敲击不耗时
请返回勇者最多能够敲击多少只地鼠。


```
impl Solution {
    pub fn get_maximum_number(moles: Vec<Vec<i32>>) -> i32 {
        // 第 0 秒锤子在中心 (1,1)，如果那里有地鼠则先额外计 1
        let mut center_hit = 0;
        let mut events: Vec<[i32; 3]> = Vec::new();

        for m in &moles {
            if m[0] == 0 {
                if m[1] == 1 && m[2] == 1 {
                    center_hit = 1;
                }
            } else {
                events.push([m[0], m[1], m[2]]);
            }
        }

        // 插入起点虚拟事件 (0, 1, 1)，作为 DP 的初始状态
        events.push([0, 1, 1]);
        events.sort_unstable();

        let n = events.len();
        // f[i]：以第 i 个事件作为最后一个敲击时的最大敲击数
        // g[i]：前 i 个事件中的最大敲击数（前缀最大值）
        let mut f = vec![0i32; n];
        let mut g = vec![0i32; n];

        for i in 1..n {
            let (ti, xi, yi) = (events[i][0], events[i][1], events[i][2]);

            for j in (0..i).rev() {
                let (tj, xj, yj) = (events[j][0], events[j][1], events[j][2]);
                let dt = ti - tj;
                let dist = (xi - xj).abs() + (yi - yj).abs();

                // 曼哈顿距离不超过 4，因此时间差 >= 4 时一定可以到达任意格子
                if dt >= 4 {
                    f[i] = f[i].max(g[j] + 1);
                    break;
                }
                if dt >= dist {
                    f[i] = f[i].max(f[j] + 1);
                }
            }

            g[i] = g[i - 1].max(f[i]);
        }

        g[n - 1] + center_hit
    }
}
```

---
title: "leetcode-动态规划183"
date: 2026-10-05T20:40:06+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 十字路口的交通

前往「力扣挑战赛」场馆的道路上，有一个拥堵的十字路口，该十字路口由两条双向两车道的路交叉构成。由于信号灯故障，交警需要手动指挥拥堵车辆。假定路口没有新的来车且一辆车从一个车道驶入另一个车道所需的时间恰好为一秒钟，长度为 4 的一维字符串数组 directions 中按照 东、南、西、北 顺序记录了四个方向从最靠近路口到最远离路口的车辆计划开往的方向。其中：

"E" 表示向东行驶；
"S" 表示向南行驶；
"W" 表示向西行驶；
"N" 表示向北行驶。

交警每秒钟只能指挥各个车道距离路口最近的一辆车，且每次指挥需要满足如下规则：

同一秒钟内，一个方向的车道只允许驶出一辆车；
同一秒钟内，一个方向的车道只允许驶入一辆车；
同一秒钟内，车辆的行驶路线不可相交。

请返回最少需要几秒钟，该十字路口等候的车辆才能全部走完。

各个车道驶出的车辆可能的行驶路线如图所示：
![](../pic/1630393755-gyPeMM-图片.png)


```
impl Solution {
    /// 将方向字符映射为编号：E=0, S=1, W=2, N=3
    #[inline]
    fn index(c: u8) -> usize {
        match c {
            b'E' => 0,
            b'S' => 1,
            b'W' => 2,
            b'N' => 3,
            _ => unreachable!(),
        }
    }

    /// 两条路线的方向是否冲突（第一类冲突）
    #[inline]
    fn check1(x: i32, y: i32) -> bool {
        ((x == 1 || x == 2) && (y == 1 || y == 2)) || (x == 3 && y == 2)
    }

    /// 两条路线的方向是否冲突（第二类冲突，对向路线）
    #[inline]
    fn check2(x: i32, y: i32) -> bool {
        (x == 1 && (y == 2 || y == 3)) || ((x == 2 || x == 3) && y == 1)
    }

    /// 同时放行的四辆车（-1 表示该车道不放行）是否互相不冲突
    #[inline]
    fn check(s0: i32, s1: i32, s2: i32, s3: i32) -> bool {
        Self::check1(s0, s1)
            || Self::check1(s1, s2)
            || Self::check1(s2, s3)
            || Self::check1(s3, s0)
            || Self::check2(s0, s2)
            || Self::check2(s1, s3)
    }

    pub fn traffic_command(directions: Vec<String>) -> i32 {
        let d: Vec<Vec<u8>> = directions.iter().map(|s| s.as_bytes().to_vec()).collect();
        let n0 = d[0].len();
        let n1 = d[1].len();
        let n2 = d[2].len();
        let n3 = d[3].len();

        const INF: i32 = i32::MAX / 2;

        // dp[i][j][k][l]：四条车道分别已放行 i,j,k,l 辆车所需的最少秒数
        // 使用一维扁平数组，内存布局与 C++ 四维数组一致：
        // 索引 = i * s_i + j * s_j + k * s_k + l
        let s_k = n3 + 1;
        let s_j = (n2 + 1) * s_k;
        let s_i = (n1 + 1) * s_j;
        let total = (n0 + 1) * s_i;
        let mut dp = vec![INF; total];
        dp[0] = 0;

        for i in 0..=n0 {
            // 车道 0 最近一辆车的行驶方向（已按本车道朝向归一化）
            let d0 = if i > 0 { Self::index(d[0][i - 1]) as i32 } else { -1 };
            for j in 0..=n1 {
                let d1 = if j > 0 { (Self::index(d[1][j - 1]) as i32 + 3) % 4 } else { -1 };
                for k in 0..=n2 {
                    let d2 = if k > 0 { (Self::index(d[2][k - 1]) as i32 + 2) % 4 } else { -1 };
                    for l in 0..=n3 {
                        let d3 = if l > 0 { (Self::index(d[3][l - 1]) as i32 + 1) % 4 } else { -1 };

                        let idx = i * s_i + j * s_j + k * s_k + l;
                        let mut ans = dp[idx];

                        // 枚举本秒放行的车辆组合（p 的 4 个比特位）
                        for p in 1u32..16 {
                            let p0 = (p & 1) as usize;
                            let p1 = (p >> 1 & 1) as usize;
                            let p2 = (p >> 2 & 1) as usize;
                            let p3 = (p >> 3 & 1) as usize;
                            if i < p0 || j < p1 || k < p2 || l < p3 {
                                continue;
                            }
                            // 冲突检测：放行的车辆之间不能有行驶路线相交
                            if !Self::check(
                                if p0 == 1 { d0 } else { -1 },
                                if p1 == 1 { d1 } else { -1 },
                                if p2 == 1 { d2 } else { -1 },
                                if p3 == 1 { d3 } else { -1 },
                            ) {
                                let prev = (i - p0) * s_i
                                    + (j - p1) * s_j
                                    + (k - p2) * s_k
                                    + (l - p3);
                                let cand = dp[prev] + 1;
                                if cand < ans {
                                    ans = cand;
                                }
                            }
                        }

                        dp[idx] = ans;
                    }
                }
            }
        }

        dp[n0 * s_i + n1 * s_j + n2 * s_k + n3]
    }
}
```

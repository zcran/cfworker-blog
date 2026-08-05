---
title: "leetcode-图50"
date: 2026-08-01T09:50:11+08:00
tags: ["leetcode", "图"]
draft: false
---


## 志愿者调配

「力扣挑战赛」有 n 个比赛场馆（场馆编号从 0 开始），场馆之间的通道分布情况记录于二维数组 edges 中，edges[i]= [x, y] 表示第 i 条通道连接场馆 x 和场馆 y(即两个场馆相邻)。初始每个场馆中都有一定人数的志愿者（不同场馆人数可能不同），后续 m 天每天均会根据赛事热度进行志愿者人数调配。调配方案分为如下三种：

1.将编号为 idx 的场馆内的志愿者人数减半；
2.将编号为 idx 的场馆相邻的场馆的志愿者人数都加上编号为 idx 的场馆的志愿者人数；
3.将编号为 idx 的场馆相邻的场馆的志愿者人数都减去编号为 idx 的场馆的志愿者人数。


所有的调配信息记录于数组 plans 中，plans[i] = [num,idx] 表示第 i 天对编号 idx 的场馆执行了第 num 种调配方案。 在比赛结束后对调配方案进行复盘时，不慎将第 0 个场馆的最终志愿者人数丢失，只保留了初始所有场馆的志愿者总人数 totalNum ，以及记录了第 1 ~ n-1 个场馆的最终志愿者人数的一维数组 finalCnt。请你根据现有的信息求出初始每个场馆的志愿者人数，并按场馆编号顺序返回志愿者人数列表。

注意：

测试数据保证当某场馆进行第一种调配时，该场馆的志愿者人数一定为偶数；
测试数据保证当某场馆进行第三种调配时，该场馆的相邻场馆志愿者人数不为负数；
测试数据保证比赛开始时每个场馆的志愿者人数都不超过 10^9；
测试数据保证给定的场馆间的道路分布情况中不会出现自环、重边的情况。


```
impl Solution {
    /// 根据最终状态反推初始每个场馆的志愿者人数
    ///
    /// # 算法原理
    /// 所有操作都是线性的，我们将最终状态中第 0 个场馆的人数视为未知数 `z`，
    /// 其他场馆的最终人数已知。从最终状态逆向执行所有计划，得到初始状态
    /// 中每个场馆人数关于 `z` 的线性表达式：`v_i = a_i * z + b_i`。
    /// 最后利用总人数约束 `sum(v_i) = total_num` 解出 `z`，即可得到初始人数。
    ///
    /// # 复杂度
    /// - 时间：O(n + m + len(plans))，其中 m 为边数
    /// - 空间：O(n + m)
    pub fn volunteer_deployment(
        final_cnt: Vec<i32>,
        total_num: i64,
        edges: Vec<Vec<i32>>,
        plans: Vec<Vec<i32>>,
    ) -> Vec<i32> {
        let n = final_cnt.len() + 1;

        // 构建邻接表
        let mut graph = vec![Vec::new(); n];
        for edge in edges {
            let u = edge[0] as usize;
            let v = edge[1] as usize;
            graph[u].push(v);
            graph[v].push(u);
        }

        // a[i] 和 b[i] 表示初始人数 v_i = a_i * z + b_i
        // 其中 z = 最终状态下场馆 0 的人数（未知）
        let mut a = vec![0_i64; n];
        let mut b = vec![0_i64; n];
        a[0] = 1;          // v_0 = 1*z + 0
        // 其他场馆最终人数已知，作为常数项
        for (i, &cnt) in final_cnt.iter().enumerate() {
            b[i + 1] = cnt as i64;
        }

        // 逆向执行所有计划（从后往前）
        for plan in plans.iter().rev() {
            let op = plan[0];
            let idx = plan[1] as usize;
            match op {
                1 => {
                    // 正向：减半 -> 逆向：翻倍
                    a[idx] *= 2;
                    b[idx] *= 2;
                }
                2 => {
                    // 正向：邻居 += idx 人数 -> 逆向：邻居 -= idx 人数
                    let ai = a[idx];
                    let bi = b[idx];
                    for &nei in &graph[idx] {
                        a[nei] -= ai;
                        b[nei] -= bi;
                    }
                }
                3 => {
                    // 正向：邻居 -= idx 人数 -> 逆向：邻居 += idx 人数
                    let ai = a[idx];
                    let bi = b[idx];
                    for &nei in &graph[idx] {
                        a[nei] += ai;
                        b[nei] += bi;
                    }
                }
                _ => unreachable!(),
            }
        }

        // 总人数方程： sum(a_i) * z + sum(b_i) = total_num
        let sum_a: i64 = a.iter().sum();
        let sum_b: i64 = b.iter().sum();

        // 解出 z（测试数据保证有唯一整数解）
        let z = (total_num - sum_b) / sum_a;

        // 计算初始人数
        let mut result = Vec::with_capacity(n);
        for i in 0..n {
            result.push((a[i] * z + b[i]) as i32);
        }
        result
    }
}
```

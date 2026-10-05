---
title: "leetcode-位运算84"
date: 2026-09-24T10:33:39+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 电梯请求 III

给你一个整数 n 表示一栋建筑的楼层数，楼层编号从 0 到 n - 1 。

同时给你一个整数 start ，表示电梯的起始楼层，以及一个二维整数数组 requests ，其中 requests[i] = [arrivali, floori] 表示在时间 arrivali 发出了一个前往楼层 floori 的请求。

在时间 0 ，电梯在楼层 start 。

每一秒钟，电梯可以 向上 移动一层、向下 移动一层，或者 停留 在当前楼层。

一个请求 只能 在其到达时间或之后被处理；从请求到达时起，只要电梯在任意时刻位于该请求对应的楼层，该请求就会被 立即 处理。

返回处理所有请求所需的 最短 时间。


```
impl Solution {
    pub fn elevator_requests(n: i32, start: i32, mut requests: Vec<Vec<i32>>) -> i64 {
        // 区间 DP：处理完的请求按楼层排序后构成连续区间 [i, j]，
        // 电梯必停在该区间的左端 requests[i] 或右端 requests[j]。
        //
        // 加入两个哨兵楼层 -1 和 n（到达时间 0），并按楼层排序，
        // 使有效请求下标为 [1, m-2]。
        //
        // f[j][0]：已处理区间 [i, j]（i 为当前外层循环左端），电梯停在左端的最早时间
        // f[j][1]：同上，停在右端的最早时间

        const INF: i64 = i64::MAX / 2;

        requests.push(vec![0, -1]);
        requests.push(vec![0, n]);
        requests.sort_unstable_by_key(|r| r[1]);

        let m = requests.len();
        // f[j] = [停在左端时间, 停在右端时间]
        let mut f = vec![[INF, INF]; m];

        // 外层 i 从右往左，内层 j 从右往左（对应原 C++ 的双重逆序）
        for i in 1..m - 1 {
            let ti = requests[i][0] as i64;
            let xi = requests[i][1] as i64;
            for j in (i..=m - 2).rev() {
                let tj = requests[j][0] as i64;
                let yj = requests[j][1] as i64;

                if i == 1 && j == m - 2 {
                    // 第一个处理的区间：从 start 出发
                    f[j][0] = (xi - start as i64).abs().max(ti);
                    f[j][1] = (yj - start as i64).abs().max(tj);
                    continue;
                }

                let left_floor = requests[i - 1][1] as i64;
                let right_floor = requests[j + 1][1] as i64;

                // 停在右端：
                //  - 从当前左端 f[j][0] 走到 yj
                //  - 从右邻 f[j+1][1]（区间 [i, j+1]）走到 yj
                f[j][1] = (f[j][0] + (yj - left_floor).abs()).max(tj)
                    .min((f[j + 1][1] + (right_floor - yj).abs()).max(tj));

                // 停在左端：
                //  - 从左邻 f[j][0]（区间 [i-1, j]）走到 xi
                //  - 从右端 f[j+1][1] 走到 xi
                f[j][0] = (f[j][0] + (xi - left_floor).abs()).max(ti)
                    .min((f[j + 1][1] + (right_floor - xi).abs()).max(ti));
            }
        }

        // 枚举最后一个完成的请求，答案为 f[i][0] 的最小值（区间 [i, m-2]）
        (1..m - 1).map(|i| f[i][0]).min().unwrap()
    }
}
```

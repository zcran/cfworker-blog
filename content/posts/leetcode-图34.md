---
title: "leetcode-图34"
date: 2026-07-27T10:01:37+08:00
tags: ["leetcode", "图"]
draft: false
---


## 按距离统计房屋对数目 II

给你三个 正整数 n 、x 和 y 。

在城市中，存在编号从 1 到 n 的房屋，由 n 条街道相连。对所有 1 <= i < n ，都存在一条街道连接编号为 i 的房屋与编号为 i + 1 的房屋。另存在一条街道连接编号为 x 的房屋与编号为 y 的房屋。

对于每个 k（1 <= k <= n），你需要找出所有满足要求的 房屋对 [house1, house2] ，即从 house1 到 house2 需要经过的 最少 街道数为 k 。

返回一个下标从 1 开始且长度为 n 的数组 result ，其中 result[k] 表示所有满足要求的房屋对的数量，即从一个房屋到另一个房屋需要经过的 最少 街道数为 k 。

注意，x 与 y 可以 相等 。


```
impl Solution {
    pub fn count_of_pairs(n: i32, x: i32, y: i32) -> Vec<i64> {
        let n = n as usize;
        let (mut x, mut y) = (x as usize, y as usize);
        if x > y {
            std::mem::swap(&mut x, &mut y);
        }

        // 差分数组，下标表示距离（1..n）
        let mut diff = vec![0; n + 2];

        // 区间加，l 和 r 是距离值（1-based）
        let add = |diff: &mut [i32], l: i32, r: i32, v: i32| {
            if l > r || r < 1 || l > n as i32 {
                return;
            }
            let l = l.max(1) as usize;
            let r = r.min(n as i32) as usize;
            if l > r {
                return;
            }
            diff[l] += v;
            diff[r + 1] -= v;
        };

        // 处理起点在额外边左侧的情况（i <= x）
        let update_left = |diff: &mut [i32], i: usize, x: usize, y: usize| {
            let i = i as i32;
            let x = x as i32;
            let y = y as i32;
            let n = n as i32;

            // 撤销原先线性路径到 [y, n] 的距离贡献
            add(diff, y - i, n - i, -1);
            let dec = y - x - 1;
            // 新增通过额外边缩短后的距离贡献
            add(diff, y - i - dec, n - i - dec, 1);

            // 处理中间区域
            let j = (x + y + 1) / 2 + 1;
            add(diff, j - i, y - 1 - i, -1);
            add(diff, x - i + 2, x - i + y - j + 1, 1);
        };

        // 处理起点在额外边中间的情况（x < i < y）
        let update_mid = |diff: &mut [i32], i: usize, x: usize, y: usize| {
            let i = i as i32;
            let x = x as i32;
            let y = y as i32;
            let n = n as i32;

            add(diff, y - i, n - i, -1);
            let dec = (y - i) - (i - x + 1);
            add(diff, y - i - dec, n - i - dec, 1);

            let j = i + (y - x + 1) / 2 + 1;
            add(diff, j - i, y - 1 - i, -1);
            add(diff, i - x + 2, i - x + y - j + 1, 1);
        };

        // 对称映射：将右侧起点映射到左侧
        let symmetric = |i: usize| -> (usize, usize, usize) {
            (n + 1 - i, n + 1 - y, n + 1 - x)
        };

        for i in 1..=n {
            // 基础线性街道贡献：到左侧和右侧节点的距离分布
            add(&mut diff, 1, (i - 1) as i32, 1);
            add(&mut diff, 1, (n - i) as i32, 1);

            // 如果额外边没有缩短距离，无需修正
            if x + 1 >= y {
                continue;
            }

            if i <= x {
                update_left(&mut diff, i, x, y);
            } else if i >= y {
                let (si, sx, sy) = symmetric(i);
                update_left(&mut diff, si, sx, sy);
            } else if i < (x + y) / 2 {
                update_mid(&mut diff, i, x, y);
            } else if i > (x + y + 1) / 2 {
                let (si, sx, sy) = symmetric(i);
                update_mid(&mut diff, si, sx, sy);
            }
        }

        // 前缀和得到每个距离的出现次数
        let mut ans = vec![0i64; n];
        let mut sum = 0i64;
        for k in 1..=n {
            sum += diff[k] as i64;
            ans[k - 1] = sum;
        }
        ans
    }
}
```

---
title: "leetcode-排序43"
date: 2026-10-05T00:35:02+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 最高建筑高度

在一座城市里，你需要建 n 栋新的建筑。这些新的建筑会从 1 到 n 编号排成一列。

这座城市对这些新建筑有一些规定：

每栋建筑的高度必须是一个非负整数。
第一栋建筑的高度 必须 是 0 。
任意两栋相邻建筑的高度差 不能超过  1 。

除此以外，某些建筑还有额外的最高高度限制。这些限制会以二维整数数组 restrictions 的形式给出，其中 restrictions[i] = [idi, maxHeighti] ，表示建筑 idi 的高度 不能超过 maxHeighti 。

题目保证每栋建筑在 restrictions 中 至多出现一次 ，同时建筑 1 不会 出现在 restrictions 中。

请你返回 最高 建筑能达到的 最高高度 。


```
impl Solution {
    /// 返回最高建筑能达到的最高高度。
    ///
    /// 思路：
    /// 1. 建筑高度受相邻差 ≤ 1 约束，且建筑 1 高度固定为 0。
    /// 2. 将 (1, 0) 和 (n, n-1) 作为哨兵限制加入，并按位置排序。
    /// 3. 先从左到右、再从右到左传播限制，得到每栋受限建筑的最终最高高度。
    /// 4. 对每对相邻受限建筑，两限制之间可形成的峰值高度为
    ///    (dist + h_left + h_right) / 2，取最大值即为答案。
    pub fn max_building(n: i32, restrictions: Vec<Vec<i32>>) -> i32 {
        // 加入哨兵并排序
        let mut r = restrictions;
        r.push(vec![1, 0]);
        r.sort_unstable_by_key(|x| x[0]);

        // 若末尾不是 n，补上哨兵 (n, n-1)
        if r.last().unwrap()[0] != n {
            r.push(vec![n, n - 1]);
        }

        let m = r.len();

        // 从左向右传播限制
        for i in 1..m {
            let dist = r[i][0] - r[i - 1][0];
            r[i][1] = r[i][1].min(r[i - 1][1] + dist);
        }

        // 从右向左传播限制
        for i in (0..m - 1).rev() {
            let dist = r[i + 1][0] - r[i][0];
            r[i][1] = r[i][1].min(r[i + 1][1] + dist);
        }

        // 计算相邻受限建筑之间可达到的最高峰值
        (0..m - 1)
            .map(|i| {
                let dist = r[i + 1][0] - r[i][0];
                (dist + r[i][1] + r[i + 1][1]) / 2
            })
            .max()
            .unwrap()
    }
}
```

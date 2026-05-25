---
title: "leetcode-几何37"
date: 2026-05-11T20:20:56+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 玩具套圈

「力扣挑战赛」场地外，小力组织了一个套玩具的游戏。所有的玩具摆在平地上，toys[i] 以 [xi,yi,ri] 的形式记录了第 i 个玩具的坐标 (xi,yi) 和半径 ri。小扣试玩了一下，他扔了若干个半径均为 r 的圈，circles[j] 记录了第 j 个圈的坐标 (xj,yj)。套圈的规则如下：

若一个玩具被某个圈完整覆盖了（即玩具的任意部分均在圈内或者圈上），则该玩具被套中。
若一个玩具被多个圈同时套中，最终仅计算为套中一个玩具
请帮助小扣计算，他成功套中了多少玩具。

注意：

输入数据保证任意两个玩具的圆心不会重合，但玩具之间可能存在重叠。

```
use std::collections::HashMap;

impl Solution {
    /// 计算有多少玩具可以被至少一个半径为 `r` 的圆盘完全覆盖。
    /// 玩具由 `(x, y, radius)` 表示，圆盘由 `(x, y)` 表示（半径均为 `r`）。
    /// 一个玩具能被覆盖当且仅当存在一个圆盘圆心 `(cx, cy)` 满足：
    ///     |cx - tx|^2 + |cy - ty|^2 <= (r - toy_radius)^2
    pub fn circle_game(toys: Vec<Vec<i32>>, circles: Vec<Vec<i32>>, r: i32) -> i32 {
        // 将圆盘按 x 坐标分组，每组内的 y 坐标排序以便二分查找
        let mut groups: HashMap<i32, Vec<i32>> = HashMap::new();
        for circ in circles {
            let x = circ[0];
            let y = circ[1];
            groups.entry(x).or_insert_with(Vec::new).push(y);
        }
        // 对每组内的 y 排序
        for ys in groups.values_mut() {
            ys.sort_unstable();
        }

        // 辅助函数：检查是否存在圆盘能覆盖给定玩具
        let is_covered = |toy: &[i32]| -> bool {
            let tx = toy[0];
            let ty = toy[1];
            let tr = toy[2];
            if tr > r {
                return false;
            }
            let d = r - tr;          // 圆心之间允许的最大距离
            let x_min = tx - d;
            let x_max = tx + d;
            // 遍历所有可能的 x 坐标（圆盘圆心 x 值必须位于此区间）
            (x_min..=x_max).any(|cx| {
                let dx = (cx - tx).abs();
                // 计算在当前 dx 下允许的 y 偏移量：sqrt(d^2 - dx^2)
                let dy_max = ((d * d - dx * dx) as f64).sqrt().floor() as i32;
                let y_min = ty - dy_max;
                let y_max = ty + dy_max;
                // 从 groups 中取出该 x 对应的 y 列表
                groups.get(&cx).map_or(false, |ys| {
                    // 二分查找是否存在 y 在 [y_min, y_max] 内
                    match ys.binary_search(&y_min) {
                        Ok(i) => true,        // 正好有 y == y_min
                        Err(i) => i < ys.len() && ys[i] <= y_max,
                    }
                })
            })
        };

        // 统计满足条件的玩具数量
        toys.iter().filter(|toy| is_covered(toy)).count() as i32
    }
}
```

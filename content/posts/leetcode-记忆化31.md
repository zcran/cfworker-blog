---
title: "leetcode-记忆化31"
date: 2026-05-06T20:22:34+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 使 X 和 Y 相等的最少操作次数

给你两个正整数 x 和 y 。

一次操作中，你可以执行以下四种操作之一：

1.如果 x 是 11 的倍数，将 x 除以 11 。
2.如果 x 是 5 的倍数，将 x 除以 5 。
3.将 x 减 1 。
4.将 x 加 1 。

请你返回让 x 和 y 相等的 最少 操作次数。

```
impl Solution {
    /// 返回将 `x` 变为 `y` 所需的最少操作次数。
    /// 允许的操作：
    /// - 如果当前数能被 11 整除，可以除以 11；
    /// - 如果当前数能被 5 整除，可以除以 5；
    /// - 加 1；
    /// - 减 1。
    ///
    /// 当 `x <= y` 时，直接返回 `y - x`（只有 +1 操作）。
    /// 否则使用 BFS 逐层搜索，并利用 `v < y` 时的下界进行剪枝。
    pub fn minimum_operations_to_make_equal(x: i32, y: i32) -> i32 {
        // 如果 x 已经小于等于 y，只需不断 +1
        if x <= y {
            return y - x;
        }

        let mut ans = x - y;                     // 初始上界：全用 -1 操作
        let max_val = (x + ans) as usize + 5;    // 搜索过程中可能出现的最大值（安全边界）
        let mut visited = vec![false; max_val];  // 访问标记数组
        let mut cur_layer = vec![x];             // 当前 BFS 层的节点
        let mut step = 0;                        // 当前层距离起点的步数
        visited[x as usize] = true;

        while !cur_layer.is_empty() {
            let mut next_layer = Vec::new();

            for &v in &cur_layer {
                // 到达目标，返回当前最小步数（可能已经通过剪枝更新过 ans）
                if v == y {
                    return ans.min(step);
                }

                // ----- 1. 如果可被 11 整除 -----
                if v % 11 == 0 {
                    let nv = v / 11;
                    if nv < y {
                        // 后续只需不断 +1，总步数为 (step+1) + (y - nv)
                        ans = ans.min(step + 1 + (y - nv));
                    } else if (nv as usize) < max_val && !visited[nv as usize] {
                        visited[nv as usize] = true;
                        next_layer.push(nv);
                    }
                }

                // ----- 2. 如果可被 5 整除 -----
                if v % 5 == 0 {
                    let nv = v / 5;
                    if nv < y {
                        ans = ans.min(step + 1 + (y - nv));
                    } else if (nv as usize) < max_val && !visited[nv as usize] {
                        visited[nv as usize] = true;
                        next_layer.push(nv);
                    }
                }

                // ----- 3. 减 1 -----
                let nv = v - 1;
                if nv < y {
                    ans = ans.min(step + 1 + (y - nv));
                } else if (nv as usize) < max_val && !visited[nv as usize] {
                    visited[nv as usize] = true;
                    next_layer.push(nv);
                }

                // ----- 4. 加 1 -----
                let nv = v + 1;
                if nv < y {
                    ans = ans.min(step + 1 + (y - nv));
                } else if (nv as usize) < max_val && !visited[nv as usize] {
                    visited[nv as usize] = true;
                    next_layer.push(nv);
                }
            }

            cur_layer = next_layer;
            step += 1;
        }

        ans
    }
}
```

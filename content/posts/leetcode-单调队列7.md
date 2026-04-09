---
title: "leetcode-单调队列7"
date: 2026-03-31T20:55:29+08:00
tags: ["leetcode", "单调队列"]
draft: false
---


## 从仓库到码头运输箱子

你有一辆货运卡车，你需要用这一辆车把一些箱子从仓库运送到码头。这辆卡车每次运输有 箱子数目的限制 和 总重量的限制 。

给你一个箱子数组 boxes 和三个整数 portsCount, maxBoxes 和 maxWeight ，其中 boxes[i] = [ports​​i​, weighti] 。

ports​​i 表示第 i 个箱子需要送达的码头， weightsi 是第 i 个箱子的重量。
portsCount 是码头的数目。
maxBoxes 和 maxWeight 分别是卡车每趟运输箱子数目和重量的限制。
箱子需要按照 数组顺序 运输，同时每次运输需要遵循以下步骤：

卡车从 boxes 队列中按顺序取出若干个箱子，但不能违反 maxBoxes 和 maxWeight 限制。
对于在卡车上的箱子，我们需要 按顺序 处理它们，卡车会通过 一趟行程 将最前面的箱子送到目的地码头并卸货。如果卡车已经在对应的码头，那么不需要 额外行程 ，箱子也会立马被卸货。
卡车上所有箱子都被卸货后，卡车需要 一趟行程 回到仓库，从箱子队列里再取出一些箱子。
卡车在将所有箱子运输并卸货后，最后必须回到仓库。

请你返回将所有箱子送到相应码头的 最少行程 次数。


```
impl Solution {
    pub fn box_delivering(boxes: Vec<Vec<i32>>, ports_count: i32, max_boxes: i32, max_weight: i32) -> i32 {
        let n = boxes.len();
        let mut dp = vec![0; n + 1];

        // 使用闭包封装窗口逻辑
        let process = |(mut j, mut k, mut p, mut mb, mut mw): (usize, i32, usize, i32, i32), i: usize| {
            // 扩展窗口
            while j < n && mb > 0 && mw >= boxes[j][1] {
                mb -= 1;
                mw -= boxes[j][1];
                if j == 0 || boxes[j][0] != boxes[j - 1][0] {
                    p = j;
                    k += 1;
                }
                j += 1;
                dp[j] = 1_000_000;
            }

            // 更新 DP
            dp[j] = dp[j].min(dp[i] + k + 1);
            dp[p] = dp[p].min(dp[i] + k);

            // 准备下一轮
            mb += 1;
            mw += boxes[i][1];
            if i == n - 1 || boxes[i][0] != boxes[i + 1][0] {
                k -= 1;
            }

            (j, k, p, mb, mw)
        };

        (0..n).fold((0, 0, 0, max_boxes, max_weight), process);
        dp[n]
    }
}
```

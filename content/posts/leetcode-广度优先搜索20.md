---
title: "leetcode-广度优先搜索20"
date: 2026-09-13T09:32:07+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 蛇梯棋

给你一个大小为 n x n 的整数矩阵 board ，方格按从 1 到 n^2 编号，编号遵循 转行交替方式 ，从左下角开始 （即，从 board[n - 1][0] 开始）的每一行改变方向。

你一开始位于棋盘上的方格  1。每一回合，玩家需要从当前方格 curr 开始出发，按下述要求前进：

· 选定目标方格 next ，目标方格的编号在范围 [curr + 1, min(curr + 6, n^2)] 。
    · 该选择模拟了掷 六面体骰子 的情景，无论棋盘大小如何，玩家最多只能有 6 个目的地。
· 传送玩家：如果目标方格 next 处存在蛇或梯子，那么玩家会传送到蛇或梯子的目的地。否则，玩家传送到目标方格 next 。
· 当玩家到达编号 n^2 的方格时，游戏结束。

如果 board[r][c] != -1 ，位于 r 行 c 列的棋盘格中可能存在 “蛇” 或 “梯子”。那个蛇或梯子的目的地将会是 board[r][c]。编号为 1 和 n^2 的方格不是任何蛇或梯子的起点。

注意，玩家在每次掷骰的前进过程中最多只能爬过蛇或梯子一次：就算目的地是另一条蛇或梯子的起点，玩家也 不能 继续移动。

· 举个例子，假设棋盘是 [[-1,4],[-1,3]] ，第一次移动，玩家的目标方格是 2 。那么这个玩家将会顺着梯子到达方格 3 ，但 不能 顺着方格 3 上的梯子前往方格 4 。（简单来说，类似飞行棋，玩家掷出骰子点数后移动对应格数，遇到单向的路径（即梯子或蛇）可以直接跳到路径的终点，但如果多个路径首尾相连，也不能连续跳多个路径）

返回达到编号为 n^2 的方格所需的最少掷骰次数，如果不可能，则返回 -1。


```
impl Solution {
    pub fn snakes_and_ladders(board: Vec<Vec<i32>>) -> i32 {
        let n = board.len();
        let target = n * n;

        // visited 记录已经作为状态入队的格子编号，避免重复入队和死循环
        let mut visited = vec![false; target + 1];
        visited[1] = true;

        // BFS 逐层扩展，level 即为已掷骰次数
        let mut queue = vec![1usize];
        let mut steps = 0;

        while !queue.is_empty() {
            // 当前层能到达的所有格子，若含终点则直接返回
            let mut next_queue = Vec::with_capacity(queue.len() * 6);

            for &cur in &queue {
                if cur == target {
                    return steps;
                }

                // 掷骰：目标为 cur + 1 ..= min(cur + 6, target)
                for next in cur + 1..=(cur + 6).min(target) {
                    // 编号 -> 行列（左下角为 1，逐行交替方向）
                    let r = (next - 1) / n;          // 从下往上的行号
                    let mut c = (next - 1) % n;
                    if r % 2 == 1 {
                        c = n - 1 - c;               // 奇数行方向相反
                    }
                    let row = n - 1 - r;

                    // 若有蛇或梯子则传送到目的地，否则停留在 next
                    let dest = match board[row][c] {
                        -1 => next,
                        v => v as usize,
                    };

                    if !visited[dest] {
                        visited[dest] = true;
                        next_queue.push(dest);
                    }
                }
            }

            queue = next_queue;
            steps += 1;
        }

        -1
    }
}
```

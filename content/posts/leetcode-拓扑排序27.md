---
title: "leetcode-拓扑排序27"
date: 2026-04-26T21:00:35+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 弹珠游戏

欢迎各位来到「力扣嘉年华」，接下来将为各位介绍在活动中广受好评的弹珠游戏。

N*M 大小的弹珠盘的初始状态信息记录于一维字符串型数组 plate 中，数组中的每个元素为仅由 "O"、"W"、"E"、"." 组成的字符串。其中：

"O" 表示弹珠洞（弹珠到达后会落入洞中，并停止前进）；
"W" 表示逆时针转向器（弹珠经过时方向将逆时针旋转 90 度）；
"E" 表示顺时针转向器（弹珠经过时方向将顺时针旋转 90 度）；
"." 表示空白区域（弹珠可通行）。
游戏规则要求仅能在边缘位置的 空白区域 处（弹珠盘的四角除外）沿 与边缘垂直 的方向打入弹珠，并且打入后的每颗弹珠最多能 前进 num 步。请返回符合上述要求且可以使弹珠最终入洞的所有打入位置。你可以 按任意顺序 返回答案。

```
impl Solution {
    /// 返回所有可以放置弹球的位置（从边缘垂直进入，经过不超过 `num` 步撞到 'O'）。
    /// 优化点：
    /// - 状态压缩：将 (i, j, dir) 编码为单个整数，使用一维数组存储距离。
    /// - 使用预分配的 `VecDeque`，并设置合理容量。
    /// - 将 `plate` 转为字节切片，避免重复调用 `as_bytes`。
    /// - 内联边界检查与方向旋转。
    pub fn ball_game(num: i32, plate: Vec<String>) -> Vec<Vec<i32>> {
        let n = plate.len();
        let m = plate[0].len();
        let num = num as usize;
        // 转换为字节二维视图，提高访问速度
        let grid: Vec<&[u8]> = plate.iter().map(|s| s.as_bytes()).collect();

        const DIR: [(i32, i32); 4] = [(-1, 0), (0, 1), (1, 0), (0, -1)];
        let total_states = n * m * 4;
        let mut dist = vec![-1i32; total_states];
        let mut queue = std::collections::VecDeque::with_capacity(total_states);

        // 初始化：所有 'O' 的四个方向步数为 0
        for i in 0..n {
            for j in 0..m {
                if grid[i][j] == b'O' {
                    let base = (i * m + j) * 4;
                    for k in 0..4 {
                        dist[base + k] = 0;
                        queue.push_back(base + k);
                    }
                }
            }
        }

        // 反向 BFS
        while let Some(state) = queue.pop_front() {
            let d = dist[state];
            let i = state / (m * 4);
            let rem = state % (m * 4);
            let j = rem / 4;
            let k = rem % 4;

            let (di, dj) = DIR[k];
            let ni = i as i32 + di;
            let nj = j as i32 + dj;
            if ni < 0 || nj < 0 || ni >= n as i32 || nj >= m as i32 {
                continue;
            }
            let (ni, nj) = (ni as usize, nj as usize);
            let mut nk = k;
            match grid[ni][nj] {
                b'W' => nk = (nk + 1) & 3, // 模 4 用位运算加速
                b'E' => nk = (nk + 3) & 3,
                _ => {}
            }
            let nstate = (ni * m + nj) * 4 + nk;
            if dist[nstate] == -1 {
                dist[nstate] = d + 1;
                queue.push_back(nstate);
            }
        }

        let mut ans = Vec::new();
        // 辅助函数：检查边缘格子是否合法
        let check = |i: usize, j: usize, k: usize| -> bool {
            if grid[i][j] != b'.' {
                return false;
            }
            let state = (i * m + j) * 4 + k;
            dist[state] != -1 && dist[state] as usize <= num
        };

        // 左边缘（j=0），方向必须向左（k=3）
        for i in 1..n - 1 {
            if check(i, 0, 3) {
                ans.push(vec![i as i32, 0]);
            }
        }
        // 右边缘（j=m-1），方向必须向右（k=1）
        for i in 1..n - 1 {
            if check(i, m - 1, 1) {
                ans.push(vec![i as i32, (m - 1) as i32]);
            }
        }
        // 上边缘（i=0），方向必须向上（k=0）
        for j in 1..m - 1 {
            if check(0, j, 0) {
                ans.push(vec![0, j as i32]);
            }
        }
        // 下边缘（i=n-1），方向必须向下（k=2）
        for j in 1..m - 1 {
            if check(n - 1, j, 2) {
                ans.push(vec![(n - 1) as i32, j as i32]);
            }
        }
        ans
    }
}
```

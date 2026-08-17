---
title: "leetcode-模拟140"
date: 2026-08-08T11:31:17+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 机器人大冒险


力扣团队买了一个可编程机器人，机器人初始位置在原点(0, 0)。小伙伴事先给机器人输入一串指令command，机器人就会无限循环这条指令的步骤进行移动。指令有两种：

U: 向y轴正方向移动一格
R: 向x轴正方向移动一格。

不幸的是，在 xy 平面上还有一些障碍物，他们的坐标用obstacles表示。机器人一旦碰到障碍物就会被损毁。

给定终点坐标(x, y)，返回机器人能否完好地到达终点。如果能，返回true；否则返回false。


C++
```
/*
模拟机器人执行一次指令的路径

将路径坐标编码存入集合，计算目标点在指令循环后的剩余坐标，验证其是否在单次路径中；

再逐一校验障碍物坐标，若障碍物坐标在机器人到达目标点前的路径中则返回false，否则返回true
*/
#include <unordered_set>
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

class Solution {
public:
    bool robot(string cmd, vector<vector<int>>& obs, int x, int y) {
        unordered_set<long> st;
        int cx = 0, cy = 0;
        st.insert(0);
        for (char c : cmd) {
            if (c == 'U') cy++;
            else if (c == 'R') cx++;
            st.insert(((long)cx << 30) | cy);
        }

        int cir = min(x / cx, y / cy);
        if (!st.count(((long)(x - cir * cx) << 30) | (y - cir * cy))) return false;

        for (auto& v : obs) {
            if (v.size() != 2) continue;
            if (v[0] > x || v[1] > y) continue;
            cir = min(v[0] / cx, v[1] / cy);
            if (st.count(((long)(v[0] - cir * cx) << 30) | (v[1] - cir * cy))) return false;
        }
        return true;
    }
};
```


Rust
```
use std::collections::HashSet;

impl Solution {
    /// 判断机器人能否完好到达终点
    ///
    /// 核心思路：
    /// 1. 预计算单次指令循环的所有路径点
    /// 2. 目标点 (x,y) 可达 ⟺ 存在 k 使得 (x-k·cx, y-k·cy) 在单次路径中
    /// 3. 障碍物在目标点之前的路径上则机器人损毁
    pub fn robot(cmd: String, obstacles: Vec<Vec<i32>>, x: i32, y: i32) -> bool {
        let mut path = HashSet::new();
        let (mut cx, mut cy) = (0, 0);
        path.insert(0u64);

        for c in cmd.bytes() {
            match c {
                b'U' => cy += 1,
                _ => cx += 1,
            }
            path.insert(encode(cx, cy));
        }

        // 检查 (px, py) 是否在无限循环路径上
        let on_path = |px: i32, py: i32| {
            let k = (px / cx).min(py / cy);
            path.contains(&encode(px - k * cx, py - k * cy))
        };

        if !on_path(x, y) {
            return false;
        }

        !obstacles.iter().any(|obs| {
            obs.len() == 2 && obs[0] <= x && obs[1] <= y && on_path(obs[0], obs[1])
        })
    }
}

/// 将二维坐标编码为 u64，避免元组哈希开销
#[inline]
fn encode(x: i32, y: i32) -> u64 {
    ((x as u64) << 32) | (y as u64)
}
```

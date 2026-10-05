---
title: "leetcode-前缀和140"
date: 2026-08-25T10:02:54+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 照亮道路的最少灯泡数

给你一个长度为 n 的整数数组 lights，表示一条路上从 0 到 n - 1 有 n 个位置。

对于每个位置 i：

· 如果 lights[i] = v，其中 v > 0，则在位置 i 有一个正常工作的灯泡，它 照亮 从 max(0, i - v) 到 min(n - 1, i + v)（包含边界）的每个位置。

· 如果 lights[i] = 0，则在位置 i 没有正常工作的灯泡。

如果一个位置被 至少 一个正常工作的灯泡照亮，则该位置是 可见的 。

你可以在 任意 位置安装 额外的 灯泡。每个安装在位置 j 的额外灯泡将照亮从 max(0, j - 1) 到 min(n - 1, j + 1)（包含边界）的位置。

返回使路上 每个 位置都可见所需安装的最少额外灯泡数量。


```
impl Solution {
    pub fn min_lights(lights: Vec<i32>) -> i32 {
        let n = lights.len();

        // 差分数组：记录每个位置被现有灯泡照亮的覆盖情况
        let mut diff = vec![0i32; n + 1];
        for i in 0..n {
            let v = lights[i];
            if v > 0 {
                let l = if i >= v as usize { i - v as usize } else { 0 };
                let r = (i + v as usize + 1).min(n);
                diff[l] += 1;
                diff[r] -= 1;
            }
        }

        let mut ans = 0;
        let mut covered = 0; // 当前位置被照亮的灯泡数量

        for i in 0..n {
            covered += diff[i];

            // 如果当前位置不可见，则必须安装一个额外灯泡
            if covered == 0 {
                ans += 1;
                covered += 1; // 当前灯泡照亮 [i, i+2]
                // 在差分数组中标记该灯泡的影响结束位置
                let end = (i + 3).min(n);
                diff[end] -= 1;
            }
        }

        ans
    }
}
```

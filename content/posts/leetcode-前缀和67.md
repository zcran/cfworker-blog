---
title: "leetcode-前缀和67"
date: 2026-08-25T10:02:50+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 收集垃圾的最少总时间

给你一个下标从 0 开始的字符串数组 garbage ，其中 garbage[i] 表示第 i 个房子的垃圾集合。garbage[i] 只包含字符 'M' ，'P' 和 'G' ，但可能包含多个相同字符，每个字符分别表示一单位的金属、纸和玻璃。垃圾车收拾 一 单位的任何一种垃圾都需要花费 1 分钟。

同时给你一个下标从 0 开始的整数数组 travel ，其中 travel[i] 是垃圾车从房子 i 行驶到房子 i + 1 需要的分钟数。

城市里总共有三辆垃圾车，分别收拾三种垃圾。每辆垃圾车都从房子 0 出发，按顺序 到达每一栋房子。但它们 不是必须 到达所有的房子。

任何时刻只有 一辆 垃圾车处在使用状态。当一辆垃圾车在行驶或者收拾垃圾的时候，另外两辆车 不能 做任何事情。

请你返回收拾完所有垃圾需要花费的 最少 总分钟数。


```
impl Solution {
    pub fn garbage_collection(garbage: Vec<String>, travel: Vec<i32>) -> i32 {
        let mut total_time = 0;
        let mut last_pos = [0; 3]; // [M, P, G] 每种垃圾最后出现的位置

        // 统计每种垃圾出现的次数（每单位垃圾需要1分钟收拾）
        for (i, g) in garbage.iter().enumerate() {
            total_time += g.len() as i32;

            // 记录每种垃圾最后出现的位置
            for c in g.chars() {
                match c {
                    'M' => last_pos[0] = i,
                    'P' => last_pos[1] = i,
                    'G' => last_pos[2] = i,
                    _ => {}
                }
            }
        }

        // 每辆垃圾车只需开到该种垃圾最后出现的位置
        // 行驶时间 = 从房子0到该位置的 travel 之和
        for &pos in &last_pos {
            // pos 表示房子编号，需要 travel[0..pos] 的累积和
            total_time += travel.iter().take(pos).sum::<i32>();
        }

        total_time
    }
}
```

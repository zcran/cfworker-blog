---
title: "leetcode-动态规划22"
date: 2026-10-05T20:39:55+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 青蛙过河

一只青蛙想要过河。 假定河流被等分为若干个单元格，并且在每一个单元格内都有可能放有一块石子（也有可能没有）。 青蛙可以跳上石子，但是不可以跳入水中。

给你石子的位置列表 stones（用单元格序号 升序 表示）， 请判定青蛙能否成功过河（即能否在最后一步跳至最后一块石子上）。开始时， 青蛙默认已站在第一块石子上，并可以假定它第一步只能跳跃 1 个单位（即只能从单元格 1 跳至单元格 2 ）。

如果青蛙上一步跳跃了 k 个单位，那么它接下来的跳跃距离只能选择为 k - 1、k 或 k + 1 个单位。 另请注意，青蛙只能向前方（终点的方向）跳跃。


```
use std::collections::HashMap;

impl Solution {
    pub fn can_cross(stones: Vec<i32>) -> bool {
        let n = stones.len();
        // 值 -> 下标，便于 O(1) 判断某位置是否有石子
        let pos: HashMap<i32, usize> = stones.iter().enumerate().map(|(i, &v)| (v, i)).collect();

        // memo[(idx, step)]：从第 idx 块石头、上次跳跃 step 步出发，能否到达终点
        let mut memo: HashMap<(usize, usize), bool> = HashMap::new();

        fn dfs(
            idx: usize,
            step: usize,
            stones: &[i32],
            pos: &HashMap<i32, usize>,
            memo: &mut HashMap<(usize, usize), bool>,
        ) -> bool {
            // 到达最后一块石子
            if idx == stones.len() - 1 {
                return true;
            }
            if let Some(&v) = memo.get(&(idx, step)) {
                return v;
            }

            let cur = stones[idx];
            let mut ok = false;
            // 下一步只能跳 step-1、step、step+1，且必须为正
            for next_step in [step.wrapping_sub(1), step, step + 1] {
                if next_step > 0 {
                    if let Some(&next_idx) = pos.get(&(cur + next_step as i32)) {
                        if dfs(next_idx, next_step, stones, pos, memo) {
                            ok = true;
                            break;
                        }
                    }
                }
            }

            memo.insert((idx, step), ok);
            ok
        }

        // 起点是第 0 块石子，第一步跳跃长度视为 0，则下一步只能跳 1
        dfs(0, 0, &stones, &pos, &mut memo)
    }
}
```

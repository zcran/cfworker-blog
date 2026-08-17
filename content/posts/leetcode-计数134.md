---
title: "leetcode-计数130"
date: 2026-08-01T10:40:58+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 珠玑妙算

珠玑妙算游戏（the game of master mind）的玩法如下。

计算机有4个槽，每个槽放一个球，颜色可能是红色（R）、黄色（Y）、绿色（G）或蓝色（B）。例如，计算机可能有RGGB 4种（槽1为红色，槽2、3为绿色，槽4为蓝色）。作为用户，你试图猜出颜色组合。打个比方，你可能会猜YRGB。要是猜对某个槽的颜色，则算一次“猜中”；要是只猜对颜色但槽位猜错了，则算一次“伪猜中”。注意，“猜中”不能算入“伪猜中”。

给定一种颜色组合solution和一个猜测guess，编写一个方法，返回猜中和伪猜中的次数answer，其中answer[0]为猜中的次数，answer[1]为伪猜中的次数。

```
impl Solution {
    /// 珠玑妙算：返回 [猜中次数, 伪猜中次数]。
    ///
    /// 思路：伪猜中 = 两字符串中各颜色出现次数的较小值之和 − 猜中次数。
    /// 即：先统计每种颜色在两字符串中的总频次，取 min 得到「颜色匹配总数」，
    /// 再减去位置也正确的「猜中」，剩余即为「伪猜中」。
    pub fn master_mind(solution: String, guess: String) -> Vec<i32> {
        let mut sol_cnt = [0i32; 4];   // R, Y, G, B
        let mut guess_cnt = [0i32; 4];
        let mut hits = 0;

        // 单次遍历：同时统计猜中次数与颜色频次
        for (s, g) in solution.bytes().zip(guess.bytes()) {
            if s == g {
                hits += 1;
            }
            sol_cnt[color_idx(s)] += 1;
            guess_cnt[color_idx(g)] += 1;
        }

        // 颜色匹配总数（含猜中）= Σ min(sol_cnt[i], guess_cnt[i])
        let total_matches: i32 = sol_cnt
            .iter()
            .zip(guess_cnt.iter())
            .map(|(a, b)| a.min(b))
            .sum();

        vec![hits, total_matches - hits]
    }
}

/// 将颜色字符映射为数组下标：R→0, Y→1, G→2, B→3
#[inline]
fn color_idx(c: u8) -> usize {
    match c {
        b'R' => 0,
        b'Y' => 1,
        b'G' => 2,
        _    => 3, // b'B'
    }
}
```

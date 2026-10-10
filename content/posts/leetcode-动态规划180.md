---
title: "leetcode-动态规划180"
date: 2026-10-05T20:40:06+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 得到恰好 N 分的最少天数

给你一个整数 n，表示目标分数。

你的分数初始为 0，每天你既可以 获得 分数，也可以 跳过 。

分数是在连胜期间获得的。在连胜的第一天，你获得 1 分，第二天获得 2 分，第三天获得 3 分，依此类推。跳过 一天将获得 零分 并 重置 连胜，因此下一次你获得分数时，将再次从 1 开始。

返回达到 恰好 为 n 的分数所需的 最少 天数（包括所有跳过的天数）。


```
const AFAR: i64 = i64::MAX / 4;

/// 三角数：1 + 2 + ... + v
#[inline(always)]
fn triad(v: i64) -> i64 {
    v * (v + 1) / 2
}

/// 返回最大的 d 使得 triad(d) <= m
#[inline(always)]
fn bloom(m: i64) -> i64 {
    let mut d = (2 * m).isqrt();
    if triad(d) > m {
        while triad(d) > m {
            d -= 1;
        }
    } else {
        while triad(d + 1) <= m {
            d += 1;
        }
    }
    d
}

/// 若 m 是三角数则返回其阶数，否则返回 0
#[inline(always)]
fn cadence(m: i64) -> i64 {
    if m <= 0 {
        return 0;
    }
    let d = bloom(m);
    if triad(d) == m { d } else { 0 }
}

/// 用两段连胜拼出 m 的最少天数
#[inline(always)]
fn duet(m: i64, ceiling: i64) -> i64 {
    if m <= 0 {
        return AFAR;
    }
    let mut best = AFAR;
    let mut rise = 1i64;
    let mut t = 1i64;
    while t < m {
        let np = rise + 1;
        if np >= best || np >= ceiling {
            break;
        }
        let fall = cadence(m - t);
        if fall > 0 {
            let s = rise + fall;
            if s < best {
                best = s;
            }
        }
        rise += 1;
        t += rise;
    }
    best
}

/// 用三段连胜拼出 m 的最少天数
#[inline(always)]
fn trio(m: i64, mut resonance: i64) -> i64 {
    let mut flourish = 1i64;
    let mut t = 1i64;
    while t < m {
        if flourish + 5 >= resonance {
            break;
        }
        let rest = duet(m - t, resonance - flourish - 3);
        let cand = rest + flourish + 3;
        if cand < resonance {
            resonance = cand;
        }
        flourish += 1;
        t += flourish;
    }
    resonance
}

/// 主启发式：单段 / 两段 / 三段连胜的最优组合
#[inline(always)]
fn cantus(m: i64) -> i64 {
    let solo = cadence(m);
    let mut resonance = if solo > 0 { solo + 1 } else { AFAR };
    let pair = duet(m, resonance);
    if pair + 2 < resonance {
        resonance = pair + 2;
    }
    trio(m, resonance)
}

/// 考虑结尾处跳过若干天的修正
#[inline(always)]
fn coda(m: i64) -> i64 {
    if m == 0 {
        return 0;
    }
    let mut resonance = cantus(m);
    if resonance == bloom(m) + 1 {
        return resonance;
    }
    if m >= 1 {
        let v1 = cantus(m - 1) + 2;
        if v1 < resonance {
            resonance = v1;
        }
        if resonance == bloom(m - 1) + 3 {
            return resonance;
        }
    }
    if m >= 2 {
        let v2 = cantus(m - 2) + 4;
        if v2 < resonance {
            resonance = v2;
        }
        if resonance == bloom(m - 2) + 5 {
            return resonance;
        }
    }
    if m >= 3 {
        let v3 = cantus(m - 3) + 6;
        if v3 < resonance {
            resonance = v3;
        }
    }
    resonance
}

impl Solution {
    pub fn min_days(n: i32) -> i32 {
        let chorus = n as i64;
        // 用最大的三角数 horizon 作为第一段，剩余部分递归求解
        let horizon = bloom(chorus);
        let mut zenith = horizon + coda(chorus - triad(horizon));
        // 尝试少用一天连胜，换取更小的剩余
        if horizon >= 2 {
            let descent = horizon - 1 + coda(chorus - triad(horizon - 1));
            if descent < zenith {
                zenith = descent;
            }
        }
        // 最后再尝试用两段连胜直接拼出整体
        let pair = duet(chorus, zenith);
        if pair + 1 < zenith {
            zenith = pair + 1;
        }
        zenith as i32
    }
}
```

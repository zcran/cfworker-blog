---
title: "leetcode-动态规划176"
date: 2026-10-05T20:40:06+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 转换字符串的最小成本 III

给你两个字符串 source 和 target。

同时给你一个二维字符串数组 rules，其中 rules[i] = [patterni, replacementi]，以及一个整数数组 costs，其中 costs[i] 是应用 rules[i] 的基本成本。两个数组长度相同。此外，patterni 和 replacementi 的长度也相同。

你可以 任意 次数地应用 任意 规则。每次应用规则 rule[i] 的过程如下：

· 选择当前字符串的一个下标 l，使得从 l 到 l + patterni.length - 1 的位置范围存在于当前字符串中，并且这些位置中 没有 任何一个在之前的规则应用中被使用过。
· 对于 patterni 每个下标 j，字符 patterni[j] 必须 等于 当前字符串位置 l + j 处的字符，或者是 '*'。
· 将该范围内的字符替换为 replacementi。替换内容将 完全 按照给定的使用，且不包含通配符。
· 这次规则应用的成本是 costs[i] 加上 patterni 中 '*' 字符的数量。
· 一旦某个字符位置在某次规则应用中被使用，它就 不能 在 后续 的任何规则应用中被再次使用。

因为每个 patterni 和 replacementi 的长度都相同，所以在每次规则应用之后，字符的位置都会保留。

返回将 source 转换为 target 所需的 最小 总成本。如果无法完成转换，则返回 -1。


```
impl Solution {
    pub fn min_cost(
        source: String,
        target: String,
        rules: Vec<Vec<String>>,
        costs: Vec<i32>,
    ) -> i32 {
        let s = source.as_bytes();
        let t = target.as_bytes();
        let n = s.len();

        // 预处理每条规则：加上 pattern 中 '*' 的数量，得到实际成本
        // 同时把 pattern / replacement 转成字节切片，避免反复分配
        let rules: Vec<(&[u8], &[u8], i32)> = rules
            .iter()
            .zip(&costs)
            .map(|(rule, &c)| {
                let pat = rule[0].as_bytes();
                let rep = rule[1].as_bytes();
                let star_cnt = pat.iter().filter(|&&b| b == b'*').count() as i32;
                (pat, rep, c + star_cnt)
            })
            .collect();

        const INF: i32 = i32::MAX;
        let mut f = vec![INF; n + 1];
        f[0] = 0;

        for i in 0..n {
            if f[i] == INF {
                continue;
            }

            // 不替换下标 i：s[i] 必须等于 t[i]
            if s[i] == t[i] && f[i] < f[i + 1] {
                f[i + 1] = f[i];
            }

            // 尝试用规则替换子串 [i, i+m-1]
            for &(pat, rep, cost) in &rules {
                let m = rep.len();
                let r = i + m;
                if r > n {
                    continue;
                }
                // 替换结果必须与 target 对应片段一致
                if &t[i..r] != rep {
                    continue;
                }
                // pattern 必须能匹配 source 对应片段（'*' 通配）
                if !pat
                    .iter()
                    .zip(&s[i..r])
                    .all(|(&p, &c)| p == b'*' || p == c)
                {
                    continue;
                }
                let new_cost = f[i] + cost;
                if new_cost < f[r] {
                    f[r] = new_cost;
                }
            }
        }

        if f[n] == INF { -1 } else { f[n] }
    }
}
```

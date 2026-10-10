---
title: "leetcode-动态规划146"
date: 2026-10-05T20:40:04+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 变成好标题的最少代价

给你一个长度为 n 的字符串 caption 。如果字符串中 每一个 字符都位于连续出现 至少 3 次 的组中，那么我们称这个字符串是 好 标题。

比方说：

"aaabbb" 和 "aaaaccc" 都是 好 标题。
"aabbb" 和 "ccccd" 都 不是 好标题。

你可以对字符串执行以下操作 任意 次：

选择一个下标 i（其中 0 <= i < n ）然后将该下标处的字符变为：

该字符在字母表中 前 一个字母（前提是 caption[i] != 'a' ）
该字符在字母表中 后 一个字母（caption[i] != 'z' ）

你的任务是用 最少 操作次数将 caption 变为 好 标题。如果存在 多种 好标题，请返回它们中 字典序最小 的一个。如果 无法 得到好标题，请你返回一个空字符串 "" 。

在字符串 a 和 b 中，如果两个字符串第一个不同的字符处，字符串 a 的字母比 b 的字母在字母表里出现的顺序更早，那么我们称字符串 a 的 字典序 比 b 小 。如果两个字符串前 min(a.length, b.length) 个字符都相同，那么较短的一个字符串字典序比另一个字符串小。


```
impl Solution {
    pub fn min_cost_good_caption(s: String) -> String {
        let n = s.len();
        if n < 3 {
            return String::new();
        }

        const INF: i32 = i32::MAX / 2;
        let bytes = s.as_bytes();

        // f[i][j]：从位置 i 开始，且强制位置 i 的字符为 ('a'+j) 时的最小操作次数
        // min_j[i]：f[i][*] 取到最小值时的最小 j（保证字典序最小）
        // nxt[i][j]：从状态 (i, j) 的最优转移来源颜色
        let mut f = vec![[0i32; 26]; n + 1];
        let mut min_j = vec![0usize; n + 1];
        let mut nxt = vec![[0usize; 26]; n + 1];

        for i in (0..n).rev() {
            let si = (bytes[i] - b'a') as i32;
            let mut mn = INF;
            let mut best_j = 0;

            for j in 0..26usize {
                let jj = j as i32;

                // 方案一：延续当前组（位置 i 与 i+1 同色）
                let mut res = f[i + 1][j] + (si - jj).abs();
                let mut src = j;

                // 方案二：在位置 i 开一个新的长度为 3 的组
                // 条件 i+6 <= n 确保后续至少还有 3 个字符能形成合法组，避免末尾出现长度 1 或 2 的非法组
                if i + 6 <= n {
                    let mj = min_j[i + 3];
                    let cost = f[i + 3][mj]
                        + (si - jj).abs()
                        + ((bytes[i + 1] - b'a') as i32 - jj).abs()
                        + ((bytes[i + 2] - b'a') as i32 - jj).abs();
                    if cost < res || (cost == res && mj < src) {
                        res = cost;
                        src = mj;
                    }
                }

                f[i][j] = res;
                nxt[i][j] = src;

                if res < mn {
                    mn = res;
                    best_j = j;
                }
            }
            min_j[i] = best_j;
        }

        // 回溯构造答案
        let mut ans = vec![0u8; n];
        let mut i = 0usize;
        let mut j = min_j[0];

        while i < n {
            ans[i] = b'a' + j as u8;
            let k = nxt[i][j];
            if k == j {
                // 延续，只前进一个位置
                i += 1;
            } else {
                // 开新组，填充三个相同字符
                ans[i + 1] = ans[i];
                ans[i + 2] = ans[i];
                i += 3;
                j = k;
            }
        }

        String::from_utf8(ans).unwrap()
    }
}
```

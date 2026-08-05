---
title: "leetcode-回溯29"
date: 2026-07-04T10:22:03+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 金字塔转换矩阵


你正在把积木堆成金字塔。每个块都有一个颜色，用一个字母表示。每一行的块比它下面的行 少一个块 ，并且居中。

为了使金字塔美观，只有特定的 三角形图案 是允许的。一个三角形的图案由 两个块 和叠在上面的 单个块 组成。模式是以三个字母字符串的列表形式 allowed 给出的，其中模式的前两个字符分别表示左右底部块，第三个字符表示顶部块。

例如，"ABC" 表示一个三角形图案，其中一个 “C” 块堆叠在一个 'A' 块(左)和一个 'B' 块(右)之上。请注意，这与 "BAC" 不同，"B" 在左下角，"A" 在右下角。

你从作为单个字符串给出的底部的一排积木 bottom 开始，必须 将其作为金字塔的底部。

在给定 bottom 和 allowed 的情况下，如果你能一直构建到金字塔顶部，使金字塔中的 每个三角形图案 都是在 allowed 中的，则返回 true ，否则返回 false 。

```
use std::collections::HashSet;

impl Solution {
    /// 判断能否从底部构建金字塔到顶部
    pub fn pyramid_transition(bottom: String, allowed: Vec<String>) -> bool {
        const COLORS: usize = 7;

        // 构建转移表
        let mut trans = [[0u8; COLORS]; COLORS];
        for pat in allowed {
            let bytes = pat.as_bytes();
            let l = (bytes[0] - b'A') as usize;
            let r = (bytes[1] - b'A') as usize;
            let t = (bytes[2] - b'A') as usize;
            trans[l][r] |= 1 << t;
        }

        let bottom_bytes: Vec<u8> = bottom.bytes().map(|b| b - b'A').collect();
        let mut memo = HashSet::new();

        /// 递归构建金字塔的一层
        /// - curr: 当前层
        /// - next: 正在构建的上一层（下一层？实际上是从下往上构建）
        /// - idx: 当前构建位置
        /// - level: 当前层大小
        fn dfs(
            trans: &[[u8; COLORS]],
            curr: &[u8],
            next: &mut Vec<u8>,
            idx: usize,
            memo: &mut HashSet<(Vec<u8>, Vec<u8>)>,
        ) -> bool {
            // 如果当前层只剩 1 个块，说明到达顶部
            if curr.len() == 1 {
                return true;
            }

            // 当前层构建完成，进入上一层
            if idx == curr.len() - 1 {
                // 检查是否已经搜索过这个状态
                let key = (curr.to_vec(), next.clone());
                if memo.contains(&key) {
                    return false;
                }
                memo.insert(key);

                // 下一轮从 next 层开始构建
                let mut new_next = Vec::new();
                return dfs(trans, next, &mut new_next, 0, memo);
            }

            // 尝试在当前层位置 idx 和 idx+1 上构建顶部块
            let left = curr[idx] as usize;
            let right = curr[idx + 1] as usize;
            let tops = trans[left][right];

            for top in 0..COLORS {
                if (tops >> top) & 1 == 1 {
                    next.push(top as u8);
                    if dfs(trans, curr, next, idx + 1, memo) {
                        return true;
                    }
                    next.pop();
                }
            }

            false
        }

        let mut next = Vec::new();
        dfs(&trans, &bottom_bytes, &mut next, 0, &mut memo)
    }
}
```

---
title: "leetcode-回溯38"
date: 2026-07-04T10:22:03+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 活字印刷

你有一套活字字模 tiles，其中每个字模上都刻有一个字母 tiles[i]。返回你可以印出的非空字母序列的数目。

注意：本题中，每个活字字模只能使用一次。




```
impl Solution {
    /// 返回可以印出的所有非空字母序列的数量
    ///
    /// 思路：统计每个字母的出现次数，DFS 回溯构建序列
    /// 每次选择一个字母添加到序列中，直到无法添加为止
    pub fn num_tile_possibilities(tiles: String) -> i32 {
        let mut count = [0; 26];

        // 统计每个字母的出现次数
        for ch in tiles.bytes() {
            count[(ch - b'A') as usize] += 1;
        }

        /// DFS 回溯：返回从当前状态可以构造的所有序列数
        fn backtrack(count: &mut [i32; 26]) -> i32 {
            let mut total = 0;

            for i in 0..26 {
                if count[i] == 0 {
                    continue;
                }

                // 选择当前字母作为下一个字符
                total += 1; // 当前序列本身是一个合法序列
                count[i] -= 1;
                total += backtrack(count); // 继续构建后续字符
                count[i] += 1; // 回溯
            }

            total
        }

        backtrack(&mut count)
    }
}
```

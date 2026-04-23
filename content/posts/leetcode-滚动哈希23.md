---
title: "leetcode-滚动哈希23"
date: 2026-04-17T20:00:32+08:00
tags: ["leetcode", "滚动哈希"]
draft: false
---


## 形成目标字符串需要的最少字符串数 II

给你一个字符串数组 words 和一个字符串 target。

如果字符串 x 是 words 中 任意 字符串的 前缀，则认为 x 是一个 有效 字符串。

现计划通过 连接 有效字符串形成 target ，请你计算并返回需要连接的 最少 字符串数量。如果无法通过这种方式形成 target，则返回 -1。

```
impl Solution {
    /// 计算字符串 `s` 的 Z 函数数组。
    /// `z[i]` 表示 `s` 与 `s[i..]` 的最长公共前缀长度（LCP）。
    /// 时间复杂度 O(n)，用于快速找出每个位置能匹配的前缀长度。
    fn compute_z_array(s: &[u8]) -> Vec<usize> {
        let n = s.len();
        let mut z = vec![0; n];
        z[0] = n; // 整个字符串与自身的最长公共前缀自然是 n

        // 维护一个滑动窗口 [left, right]，该窗口内的子串是当前已知的、与 s 前缀匹配的最右区间
        let mut left = 0;
        let mut right = 0;

        for i in 1..n {
            // 如果 i 落在已知匹配区间内，可以重用之前计算的结果
            if i <= right {
                // z[i - left] 是相对于窗口起始位置的已知 LCP
                // 但不能超过窗口剩余长度 (right - i + 1)
                z[i] = z[i - left].min(right - i + 1);
            }

            // 朴素扩展：尝试匹配更多字符
            while i + z[i] < n && s[z[i]] == s[i + z[i]] {
                z[i] += 1;
                // 更新窗口，使其始终覆盖最右的匹配位置
                left = i;
                right = i + z[i] - 1;
            }
        }
        z
    }

    /// 贪心跳跃：给定数组 `max_jumps`，`max_jumps[i]` 表示从下标 i 开始能向前跳跃的最大长度，
    /// 求到达终点（越过最后一个元素）所需的最少跳跃次数。
    /// 本质是经典“跳跃游戏 II”的贪心解法。
    /// 返回最少跳跃次数；若无法到达终点则返回 -1。
    fn minimum_jumps_to_end(max_jumps: &[usize]) -> i32 {
        let mut jumps = 0;           // 已进行的跳跃次数
        let mut current_reach = 0;   // 当前这一跳能到达的最远位置
        let mut next_reach = 0;      // 下一跳能到达的最远位置

        for (idx, &jump_len) in max_jumps.iter().enumerate() {
            // 更新下一跳能触及的最远边界
            next_reach = next_reach.max(idx + jump_len);

            // 当遍历到当前这一跳的边界时，必须增加一次跳跃
            if idx == current_reach {
                // 如果即使多跳一次也无法前进（next_reach == idx），说明卡住了，无法继续
                if idx == next_reach {
                    return -1;
                }
                // 增加一次跳跃，并更新当前边界为下一跳的最远位置
                current_reach = next_reach;
                jumps += 1;
            }
        }
        jumps
    }

    /// 给定一组单词 `words` 和目标字符串 `target`，求能够拼接出 `target` 的最少单词数。
    /// 若无法拼接，返回 -1。
    pub fn min_valid_strings(words: Vec<String>, target: String) -> i32 {
        let n = target.len();
        // max_jumps[i] 表示：从 target 的第 i 个字符开始，最远可以匹配多少个字符（覆盖长度）
        // 该数组将综合所有单词的结果，取最大值。
        let mut max_jumps = vec![0; n];
        let target_bytes = target.as_bytes();

        for word in words {
            let word_bytes = word.as_bytes();

            // 构造用于 Z 函数计算的拼接字符串：word + '#' + target
            let mut concat = Vec::with_capacity(word.len() + 1 + target.len());
            concat.extend_from_slice(word_bytes);
            concat.push(b'#'); // 分隔符，防止匹配跨越 word 和 target 的边界
            concat.extend_from_slice(target_bytes);

            // 计算拼接字符串的 Z 数组
            let z_array = Self::compute_z_array(&concat);

            // 从分隔符 '#' 之后开始，对应 target 的每一个起始位置
            // z_array[word.len() + 1 + i] 就是 target[i..] 与 word 前缀的最长匹配长度
            let start_offset = word.len() + 1;
            for (i, &z_val) in z_array[start_offset..].iter().enumerate() {
                // 对于 target 的每个位置 i，更新能够向前覆盖的最大长度
                max_jumps[i] = max_jumps[i].max(z_val);
            }
        }

        // 利用 max_jumps 数组执行贪心跳跃，求得最少单词数
        Self::minimum_jumps_to_end(&max_jumps)
    }
}
```

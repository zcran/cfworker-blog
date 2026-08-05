---
title: "leetcode-枚举79"
date: 2026-07-09T10:05:03+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 操作后最大活跃区段数 I

给你一个长度为 n 的二进制字符串 s，其中：

'1' 表示一个 活跃 区段。
'0' 表示一个 非活跃 区段。

你可以执行 最多一次操作 来最大化 s 中的活跃区段数量。在一次操作中，你可以：

将一个被 '0' 包围的连续 '1' 区块转换为全 '0'。
然后，将一个被 '1' 包围的连续 '0' 区块转换为全 '1'。

返回在执行最优操作后，s 中的 最大 活跃区段数。

注意：处理时需要在 s 的两侧加上 '1' ，即 t = '1' + s + '1'。这些加上的 '1' 不会影响最终的计数。


```
impl Solution {
    pub fn max_active_sections_after_trade(s: String) -> i32 {
        let n = s.len();
        let bytes = s.as_bytes();

        // 统计原始字符串中 '1' 的数量（即活跃区段总数）
        let initial_active = bytes.iter().filter(|&&c| c == b'1').count() as i32;

        // 找出所有连续 '0' 区块的长度
        // 例如：s = "1001001" -> zero_blocks = [2, 2]
        let mut zero_blocks = Vec::new();
        let mut i = 0;
        while i < n {
            let start = i;
            // 跳过当前连续相同字符
            while i < n && bytes[i] == bytes[start] {
                i += 1;
            }
            // 如果是 '0' 区块，记录其长度
            if bytes[start] == b'0' {
                zero_blocks.push((i - start) as i32);
            }
        }

        // 如果 '0' 区块少于 2 个，无法执行交换操作（需要至少两个连续的 '0' 区块）
        if zero_blocks.len() < 2 {
            return initial_active;
        }

        // 计算最优增量：选择相邻的两个 '0' 区块进行交换
        // 交换操作可以将这两个 '0' 区块变为 '1'，同时中间的 '1' 区块变为 '0'
        // 净增益 = 这两个 '0' 区块长度之和
        let mut max_gain = 0;
        for j in 0..zero_blocks.len() - 1 {
            let gain = zero_blocks[j] + zero_blocks[j + 1];
            max_gain = max_gain.max(gain);
        }

        // 最终活跃区段数 = 初始活跃数 + 最大增量
        initial_active + max_gain
    }
}
```

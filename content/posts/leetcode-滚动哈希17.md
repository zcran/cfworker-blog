---
title: "leetcode-滚动哈希17"
date: 2026-04-17T20:00:31+08:00
tags: ["leetcode", "滚动哈希"]
draft: false
---


## 将单词恢复初始状态所需的最短时间 I

给你一个下标从 0 开始的字符串 word 和一个整数 k 。

在每一秒，你必须执行以下操作：

移除 word 的前 k 个字符。
在 word 的末尾添加 k 个任意字符。
注意 添加的字符不必和移除的字符相同。但是，必须在每一秒钟都执行 两种 操作。

返回将 word 恢复到其 初始 状态所需的 最短 时间（该时间必须大于零）。

```
impl Solution {
    /// 计算将字符串 s 通过每次移除前 k 个字符并追加任意字符，使其恢复初始状态所需的最小操作次数。
    /// 等价于：找到最小的正整数 t，使得 s 从索引 t*k 开始的子串与 s 的前缀相同（即 s[t*k..] 是 s 的前缀）。
    /// 使用 Z 算法（Z-function）在线性时间内求解。
    pub fn minimum_time_to_initial_state(s: String, k: i32) -> i32 {
        let s = s.as_bytes();
        let n = s.len();
        let k = k as usize;

        // Z 数组：z[i] 表示 s[i..] 与 s 的最长公共前缀长度
        let mut z = vec![0; n];
        let mut l = 0;  // 当前 Z-box 的左边界
        let mut r = 0;  // 当前 Z-box 的右边界（包含）

        for i in 1..n {
            // 如果 i 在当前 Z-box 内，利用对称性初始化 z[i]
            if i <= r {
                z[i] = std::cmp::min(z[i - l], r - i + 1);
            }
            // 暴力扩展，直到不匹配或到达边界
            while i + z[i] < n && s[z[i]] == s[i + z[i]] {
                l = i;
                r = i + z[i];
                z[i] += 1;
            }
            // 检查条件：i 是 k 的倍数，且从 i 开始的子串完全匹配前缀
            if i % k == 0 && z[i] >= n - i {
                return (i / k) as i32;
            }
        }

        // 如果没有提前返回，则需要补全到完整长度
        ((n - 1) / k + 1) as i32
    }
}
```

---
title: "leetcode-双指针115"
date: 2026-09-05T09:53:08+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 从原字符串里进行删除操作的最多次数

给你一个长度为 n 的字符串 source ，一个字符串 pattern 且它是 source 的 子序列 ，和一个 有序 整数数组 targetIndices ，整数数组中的元素是 [0, n - 1] 中 互不相同 的数字。

定义一次 操作 为删除 source 中下标在 idx 的一个字符，且需要满足：

· idx 是 targetIndices 中的一个元素。
· 删除字符后，pattern 仍然是 source 的一个 子序列 。

执行操作后 不会 改变字符在 source 中的下标位置。比方说，如果从 "acb" 中删除 'c' ，下标为 2 的字符仍然是 'b' 。

请你返回 最多 可以进行多少次删除操作。

子序列指的是在原字符串里删除若干个（也可以不删除）字符后，不改变顺序地连接剩余字符得到的字符串。


```
impl Solution {
    /// 最多可执行的删除操作次数。
    ///
    /// 一次操作：删除 source 中某个下标 idx 的字符，要求 idx ∈ targetIndices，
    /// 且删除后 pattern 仍是 source 的子序列。
    ///
    /// 思路：一维 DP（滚动数组），自底向上。
    /// 令 f[j] 表示：扫描到当前位置时，已匹配 pattern 前 j 个字符（即 pattern[0..j]）时，
    /// 能获得的最大删除次数。
    /// 对每个 source[i]，从大到小更新 j（避免覆盖本轮未更新的状态）：
    ///   - 若不删除 source[i]：best = f[j] + (i ∈ targetIndices)；
    ///   - 若 source[i] == pattern[j-1]：可让 i 匹配 pattern[j-1]，best = max(best, f[j-1])。
    /// 边界 f[-1] 用 cnt 表示（cnt = 已扫描中 ∈ targetIndices 的个数）。
    ///
    /// 时间复杂度 O(n * m)，空间复杂度 O(m)。
    pub fn max_removals(s: String, p: String, idxs: Vec<i32>) -> i32 {
        let s = s.as_bytes();
        let p = p.as_bytes();
        let n = s.len();
        let m = p.len();

        // score[i] = 1 表示 i 在 targetIndices 中
        let mut score = vec![0u8; n];
        for x in idxs {
            score[x as usize] = 1;
        }

        // f[j]：已匹配 pattern[0..j] 时的最大删除次数
        let mut f = vec![i32::MIN; m];
        let mut cnt = 0; // 已扫描字符中位于 targetIndices 的数量，等价于 f[-1]

        for i in 0..n {
            let sc = score[i] as i32;
            // 从大到小更新，保证 f[j-1] 仍是上一轮（未用 s[i]）的值
            for j in (0..usize::min(i + 1, m)).rev() {
                // 方案一：不用 s[i] 匹配，尝试删除它（若在 targetIndices 中）
                let mut best = f[j] + sc;
                // 方案二：s[i] 匹配 pattern[j]
                if s[i] == p[j] {
                    let prev = if j == 0 { cnt } else { f[j - 1] };
                    if prev > best {
                        best = prev;
                    }
                }
                f[j] = best;
            }
            cnt += sc;
        }

        f.last().copied().unwrap_or(0)
    }
}
```

---
title: "leetcode-字符串匹配4"
date: 2026-04-20T20:32:10+08:00
tags: ["leetcode", "字符串匹配"]
draft: false
---


## 重复叠加字符串匹配

给定两个字符串 a 和 b，寻找重复叠加字符串 a 的最小次数，使得字符串 b 成为叠加后的字符串 a 的子串，如果不存在则返回 -1。

注意：字符串 "abc" 重复叠加 0 次是 ""，重复叠加 1 次是 "abc"，重复叠加 2 次是 "abcabc"。

```
impl Solution {
    /// 寻找将字符串 `a` 重复的最小次数，使得 `b` 成为结果字符串的子串。
    /// 若不可能，则返回 -1。
    ///
    /// # 算法思路
    /// 1. 计算最少重复次数 `n = ceil(len(b) / len(a))`，以保证总长度 ≥ len(b)。
    /// 2. 构造 `repeated = a.repeat(n)`，检查是否包含 `b`。
    /// 3. 若不包含，再尝试 `a.repeat(n+1)`，因为多复制一次可能捕获跨边界情况。
    /// 4. 若仍不包含，则永远不可能，返回 -1。
    ///
    /// # 复杂度
    /// - 时间：O(len(a) * (n+1)) 用于构造字符串和子串查找，实际为 O(len(b) + len(a))。
    /// - 空间：O(len(a) * (n+1)) 用于存储重复后的字符串。
    pub fn repeated_string_match(mut a: String, mut b: String) -> i32 {
        // 计算最小重复次数 n，向上取整以保证总长度至少为 b 的长度。
        // 使用浮点数 ceil 取整，避免整数除法截断。
        let n = (b.len() as f32 / a.len() as f32).ceil() as i32;

        // 构造重复 n 次的字符串。
        let repeated_n = a.repeat(n as usize);

        // 检查重复 n 次是否已包含 b。
        if repeated_n.contains(&b) {
            return n;
        }

        // 若不包含，再拼接一个 a（相当于重复 n+1 次）进行检查。
        // 这是必要的，因为 b 可能跨越第一个 a 的末尾和第二个 a 的开头。
        let repeated_n_plus_1 = repeated_n + &a;
        if repeated_n_plus_1.contains(&b) {
            return n + 1;
        }

        // 若都不包含，则无解。
        -1
    }
}
```

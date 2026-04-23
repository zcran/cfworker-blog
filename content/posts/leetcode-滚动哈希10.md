---
title: "leetcode-滚动哈希10"
date: 2026-04-17T20:00:31+08:00
tags: ["leetcode", "滚动哈希"]
draft: false
---


## 两个回文子字符串长度的最大乘积

给你一个下标从 0 开始的字符串 s ，你需要找到两个 不重叠的回文 子字符串，它们的长度都必须为 奇数 ，使得它们长度的乘积最大。

更正式地，你想要选择四个整数 i ，j ，k ，l ，使得 0 <= i <= j < k <= l < s.length ，且子字符串 s[i...j] 和 s[k...l] 都是回文串且长度为奇数。s[i...j] 表示下标从 i 到 j 且 包含 两端下标的子字符串。

请你返回两个不重叠回文子字符串长度的 最大 乘积。

回文字符串 指的是一个从前往后读和从后往前读一模一样的字符串。子字符串 指的是一个字符串中一段连续字符。


```
impl Solution {
    pub fn max_product(s: String) -> i64 {
        let chars = s.as_bytes();          // 转为字节数组，方便索引比较
        let n = s.len();

        // ========== 1. Manacher 算法求所有中心的最大半径 ==========
        let mut j = 0;                     // 当前最右回文中心
        let mut k = 0;                     // 当前最右回文右边界（包含）
        let mut radius = vec![0; n];       // radius[i] 表示以 i 为中心的最大回文半径（只考虑奇数长度）
        for i in 0..n {
            // 利用对称性初始化半径
            let mut r = if i <= k {
                std::cmp::min(radius[2 * j - i], k - i)
            } else {
                0
            };
            // 中心扩展
            while i >= r + 1 && i + r + 1 < n && chars[i - r - 1] == chars[i + r + 1] {
                r += 1;
            }
            // 更新最右回文
            if i + r > k {
                j = i;
                k = i + r;
            }
            radius[i] = r;
        }

        // ========== 2. 预处理 prefix 和 suffix ==========
        // prefix[i] : 在区间 [0, i-1] 内（即 i 左侧）的最长回文子串长度
        // suffix[i] : 在区间 [i, n-1] 内（即 i 右侧）的最长回文子串长度
        let mut prefix = vec![1; n + 1];   // 初始为 1（单个字符也是回文）
        let mut suffix = vec![1; n + 1];

        let mut hi = 0;    // 当前已覆盖到的最右位置
        let mut lo = n - 1; // 当前已覆盖到的最左位置

        // 从左到右更新 prefix
        for i in 0..n {
            // 对于以 i 为中心、半径为 r 的回文，其覆盖区间为 [i-r, i+r]
            // 我们希望记录：对于所有右端点 i+r，该回文长度 2r+1 可以更新 prefix[i+r+1]
            // 使用循环从 hi - i + 1 开始，避免重复更新（已经更新过的更小半径无需再处理）
            for r in (hi - i + 1)..(radius[i] + 1) {
                let end = i + r;                // 回文右端点
                let len = 1 + 2 * r;            // 回文长度
                // prefix[end+1] 表示在 end 之前（包括 end）的最长回文
                prefix[end + 1] = std::cmp::max(prefix[end + 1], len);
                hi = end;                       // 扩大已覆盖范围
            }
        }
        // 注意：上述更新后，prefix 还不是最终结果，需要从左到右传递最大值
        for i in 1..=n {
            prefix[i] = std::cmp::max(prefix[i], prefix[i - 1]);
        }

        // 从右到左更新 suffix（对称逻辑）
        for j in (0..n).rev() {
            for r in (j - lo + 1)..(radius[j] + 1) {
                let start = j - r;              // 回文左端点
                let len = 1 + 2 * r;
                suffix[start] = std::cmp::max(suffix[start], len);
                lo = start;
            }
        }
        for i in (0..n).rev() {
            suffix[i] = std::cmp::max(suffix[i], suffix[i + 1]);
        }

        // ========== 3. 枚举分割点，求最大乘积 ==========
        // 注意：prefix[i] 表示左半部分 [0, i-1] 的最长回文长度
        //       suffix[i] 表示右半部分 [i, n-1] 的最长回文长度
        // 因此枚举 i 从 1 到 n-1，乘积最大者即为答案
        (1..n)
            .map(|i| (prefix[i] as i64) * (suffix[i] as i64))
            .max()
            .unwrap()
    }
}
```



1.Manacher 算法 – 线性时间找出所有回文中心的最大半径。
2.预处理前缀和后缀最长回文长度 – 对于每个分割点，计算左半部分和右半部分的最长回文长度。
3.线性扫描求最大乘积 – 遍历每个分割点，计算 prefix[i] * suffix[i] 的最大值。

---
title: "leetcode-枚举73"
date: 2026-07-09T10:05:02+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 字符相同的最短子字符串 I

给你一个长度为 n 的二进制字符串 s 和一个整数 numOps。

你可以对 s 执行以下操作，最多 numOps 次：

选择任意下标 i（其中 0 <= i < n），并 翻转 s[i]，即如果 s[i] == '1'，则将 s[i] 改为 '0'，反之亦然。

你需要 最小化 s 的最长 相同 子字符串 的长度，相同子字符串 是指子字符串中的所有字符都 相同。

返回执行所有操作后可获得的 最小 长度。


```
impl Solution {
    pub fn min_length(s: String, num_ops: i32) -> i32 {
        // 将字符串转为字节数组便于处理
        let s = s.as_bytes().to_vec();
        let n = s.len();

        // 计算将字符串变成 0101... 模式所需的翻转次数
        // 对于位置 i，期望字符为 (i & 1) 的相反值（即 0 和 1 交替）
        let mut flips_to_pattern = 0;
        for i in 0..n {
            // s[i] & 1 得到字符的奇偶性：'0' => 0, '1' => 1
            // (i & 1) 得到位置的奇偶性
            // 如果两者不同，说明需要翻转
            if (i & 1) as u8 != s[i] & 1 {
                flips_to_pattern += 1;
            }
        }

        // 两种交替模式所需翻转次数分别为 flips_to_pattern 和 n - flips_to_pattern
        // 取较小值，如果 num_ops 足够，则可以将所有相同子串长度降至 1
        let min_flips = flips_to_pattern.min(n as i32 - flips_to_pattern);
        if num_ops >= min_flips {
            return 1;
        }

        /// 检查是否可以在给定最大长度 max_len 下，用不超过 ops 次操作完成
        ///
        /// 贪心策略：将每个连续段的长度 len 分解为若干段，每段长度不超过 max_len
        /// 每个长度为 len 的连续段需要 floor(len / (max_len + 1)) 次翻转
        fn can_achieve(s: &Vec<u8>, max_len: i32, ops: i32) -> bool {
            let n = s.len();
            let mut remaining_ops = ops;
            let mut i = 0;

            // 遍历所有连续相同字符段
            while i < n {
                let mut j = i;
                // 找到当前连续段的右边界
                while j < n && s[j] == s[i] {
                    j += 1;
                }

                let segment_len = (j - i) as i32;
                // 计算该段需要的最少翻转次数
                remaining_ops -= segment_len / (max_len + 1);

                // 如果操作次数不足，则无法实现
                if remaining_ops < 0 {
                    return false;
                }

                i = j;
            }

            true
        }

        // 二分查找最小可能的最长相同子串长度
        // 答案范围：[1, n]
        let mut left = 1;
        let mut right = n as i32;
        while left + 1 != right {
            let mid = (left + right) / 2;

            if can_achieve(&s, mid, num_ops) {
                right = mid;        // 可以做到，尝试更小的长度
            } else {
                left = mid;         // 做不到，需要更大的长度
            }
        }

        right
    }
}
```

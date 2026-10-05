---
title: "leetcode-前缀和134"
date: 2026-08-25T10:02:54+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 一次交换后的最长平衡子串

给你一个仅由字符 '0' 和 '1' 组成的二进制字符串 s。

如果一个字符串中 0 和 1 的数量 相等，则称该字符串是 平衡 字符串。

你最多可以让 s 中任意两个字符进行 一次 交换。之后，从 s 中选出一个 平衡 子串。

返回一个整数，表示你能够选取的 平衡 子串的 最大 长度。

子串 是字符串中的一个连续字符序列。


```
impl Solution {
    pub fn longest_balanced(s: String) -> i32 {
        let s = s.as_bytes();
        let n = s.len();

        // 统计 0 和 1 的总数
        let total0 = s.iter().filter(|&&c| c == b'0').count() as i32;
        let total1 = n as i32 - total0;

        // 前缀和范围: [-n, n]，映射到索引 [0, 2n]
        let offset = n;
        let size = 2 * n + 1;
        let mut first_pos = vec![-2i32; size]; // -2 表示未出现
        let mut second_pos = vec![-2i32; size];
        first_pos[offset] = -1; // 前缀和 0 出现在位置 -1

        let mut ans = 0;
        let mut sum = 0i32;

        for i in 0..n {
            sum += if s[i] == b'1' { 1 } else { -1 };
            let idx = (sum + offset as i32) as usize;

            // 记录最早和次早出现的位置（保留 -1 位置）
            if first_pos[idx] == -2 {
                first_pos[idx] = i as i32;
            } else if second_pos[idx] == -2 {
                second_pos[idx] = i as i32;
            }

            // 不交换：最长平衡子串
            ans = ans.max(i as i32 - first_pos[idx]);

            // 交换子串内的一个 '1' 和子串外的一个 '0'
            let target_idx = (sum - 2 + offset as i32) as usize;
            if target_idx < size && first_pos[target_idx] != -2 {
                let p0 = first_pos[target_idx];
                // 检查子串内是否有足够的 '1' 可交换
                if (i as i32 - p0 - 2) / 2 < total0 {
                    ans = ans.max(i as i32 - p0);
                } else if second_pos[target_idx] != -2 {
                    ans = ans.max(i as i32 - second_pos[target_idx]);
                }
            }

            // 交换子串内的一个 '0' 和子串外的一个 '1'
            let target_idx = (sum + 2 + offset as i32) as usize;
            if target_idx < size && first_pos[target_idx] != -2 {
                let p0 = first_pos[target_idx];
                if (i as i32 - p0 - 2) / 2 < total1 {
                    ans = ans.max(i as i32 - p0);
                } else if second_pos[target_idx] != -2 {
                    ans = ans.max(i as i32 - second_pos[target_idx]);
                }
            }
        }

        ans
    }
}
```

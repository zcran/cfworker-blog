---
title: "leetcode-数学99"
date: 2026-10-07T20:39:35+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 得到旋转回文字符串的最少操作次数 I

给你一个由小写英文字母组成的字符串 s 。

你可以按任意顺序执行以下操作任意次（包括零次）：

递增：选择任意一个下标 i 并将 s[i] 替换为下一个小写英文字母。'z' 之后的字母是 'a' 。
左旋：将字符串的第一个字符移动到末尾。

返回使 s 成为 回文串 所需的 最少 操作次数。

回文串 是正着读和反着读都一样的字符串。


```
impl Solution {
    pub fn min_operations(s: String) -> i32 {
        let bytes = s.as_bytes();
        let n = bytes.len();
        let mut ans = i32::MAX;

        // 枚举所有可能的左旋次数 rot（0..n），左旋成本即为 rot
        for rot in 0..n {
            let mut op = rot as i32;

            // 剪枝：当前成本已达最优，无需继续计算
            if op >= ans {
                continue;
            }

            // 检查旋转后字符串的前半部分与后半部分对应字符的距离
            for i in 0..n / 2 {
                let a = bytes[(rot + i) % n];
                let b = bytes[(rot + n - 1 - i) % n];
                let d = (a as i32 - b as i32).abs();
                // 环状字母距离：min(d, 26 - d)
                op += d.min(26 - d);
                if op >= ans {
                    break; // 剪枝
                }
            }

            if op < ans {
                ans = op;
            }
        }

        ans
    }
}
```

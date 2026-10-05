---
title: "leetcode-双指针28"
date: 2026-09-05T09:53:02+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 统计重复个数

定义 str = [s, n] 表示 str 由 n 个字符串 s 连接构成。

· 例如，str == ["abc", 3] =="abcabcabc" 。

如果可以从 s2 中删除某些字符使其变为 s1，则称字符串 s1 可以从字符串 s2 获得。

· 例如，根据定义，s1 = "abc" 可以从 s2 = "abdbec" 获得，仅需要删除加粗且用斜体标识的字符。

现在给你两个字符串 s1 和 s2 和两个整数 n1 和 n2 。由此构造得到两个字符串，其中 str1 = [s1, n1]、str2 = [s2, n2] 。

请你找出一个最大整数 m ，以满足 str = [str2, m] 可以从 str1 获得。


```
use std::collections::HashMap;

impl Solution {
    pub fn get_max_repetitions(s1: String, n1: i32, s2: String, n2: i32) -> i32 {
        let s1_b = s1.as_bytes();
        let s2_b = s2.as_bytes();
        let len1 = s1_b.len();
        let len2 = s2_b.len();

        // 记录每个s1匹配后，在s2中的位置和已匹配的s2字符总数
        let mut pos_map: HashMap<usize, (i32, i32)> = HashMap::new();
        let mut pos = 0; // 当前在s2中的位置
        let mut count = 0; // 已匹配的s2字符总数

        for i in 0..n1 {
            // 检查当前状态是否出现过（循环节检测）
            if let Some(&(prev_i, prev_count)) = pos_map.get(&pos) {
                // 计算循环节
                let cycle_len = i - prev_i;
                let cycle_count = count - prev_count;
                let remaining = n1 - i;

                // 跳过所有完整循环节
                let cycles = remaining / cycle_len;
                count += cycles * cycle_count;
                pos = pos; // 位置不变，因为循环节结束位置相同

                // 处理剩余的不完整部分
                let remain = remaining % cycle_len;
                for _ in 0..remain {
                    for &c in s1_b {
                        if c == s2_b[pos] {
                            pos += 1;
                            if pos == len2 {
                                pos = 0;
                                count += 1;
                            }
                        }
                    }
                }

                return count / n2;
            }

            // 记录当前状态
            pos_map.insert(pos, (i, count));

            // 用当前s1匹配s2
            for &c in s1_b {
                if c == s2_b[pos] {
                    pos += 1;
                    if pos == len2 {
                        pos = 0;
                        count += 1;
                    }
                }
            }
        }

        count / n2
    }
}
```

---
title: "leetcode-计数81"
date: 2026-08-01T10:40:55+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 使字符串中不同字符的数目相等

给你两个下标从 0 开始的字符串 word1 和 word2 。

一次 移动 由以下两个步骤组成：

选中两个下标 i 和 j ，分别满足 0 <= i < word1.length 和 0 <= j < word2.length ，

交换 word1[i] 和 word2[j] 。

如果可以通过 恰好一次 移动，使 word1 和 word2 中不同字符的数目相等，则返回 true ；否则，返回 false 。


```
impl Solution {
    pub fn is_it_possible(word1: String, word2: String) -> bool {
        // 统计两个字符串中每个小写字母的出现次数
        let mut cnt1 = [0; 26];
        let mut cnt2 = [0; 26];
        for &b in word1.as_bytes() {
            cnt1[(b - b'a') as usize] += 1;
        }
        for &b in word2.as_bytes() {
            cnt2[(b - b'a') as usize] += 1;
        }

        // 不同字符的数量
        let distinct1 = cnt1.iter().filter(|&&c| c > 0).count() as i32;
        let distinct2 = cnt2.iter().filter(|&&c| c > 0).count() as i32;

        // 若差值超过 2，一次交换不可能平衡（每次交换最多使差值变化 2）
        if (distinct1 - distinct2).abs() > 2 {
            return false;
        }

        // 枚举所有可能的交换：从 word1 取出字符 x，从 word2 取出字符 y
        for x in 0..26 {
            if cnt1[x] == 0 {
                continue; // word1 中没有字符 x，无法取出
            }
            for y in 0..26 {
                if cnt2[y] == 0 {
                    continue; // word2 中没有字符 y，无法取出
                }

                // 如果交换的是相同字符，集合不会改变，只有原本不同字符数相等才可行
                if x == y {
                    if distinct1 == distinct2 {
                        return true;
                    }
                    continue;
                }

                // 交换后 word1 的不同字符数：
                // 移除 x：若 cnt1[x] == 1，则减少 1；否则不变
                // 添加 y：若 cnt1[y] == 0，则增加 1；否则不变
                let new1 = distinct1
                    - if cnt1[x] == 1 { 1 } else { 0 }
                    + if cnt1[y] == 0 { 1 } else { 0 };

                // 交换后 word2 的不同字符数：
                // 移除 y：若 cnt2[y] == 1，则减少 1
                // 添加 x：若 cnt2[x] == 0，则增加 1
                let new2 = distinct2
                    - if cnt2[y] == 1 { 1 } else { 0 }
                    + if cnt2[x] == 0 { 1 } else { 0 };

                if new1 == new2 {
                    return true;
                }
            }
        }
        false
    }
}
```

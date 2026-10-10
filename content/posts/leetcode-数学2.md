---
title: "leetcode-数学2"
date: 2026-10-07T20:39:28+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 从英文中重建数字

给你一个字符串 s ，其中包含字母顺序打乱的用英文单词表示的若干数字（0-9）。按 升序 返回原始的数字。




```
impl Solution {
    /// 从打乱字母顺序的英文数字字符串中，按升序还原原始数字。
    ///
    /// 核心思路：某些字母在 0-9 的英文单词中只出现一次，可作为唯一标识：
    ///   z → zero(0), w → two(2), u → four(4), x → six(6), g → eight(8)
    /// 先据此确定 0,2,4,6,8，再从剩余字母中依次推断其余数字：
    ///   o → one(1)  (排除 0,2,4 后)
    ///   r → three(3)(排除 0,4 后)
    ///   f → five(5) (排除 4 后)
    ///   v → seven(7)(排除 5 后)
    ///   e → nine(9) (排除 0,1,3,5,7 后)
    pub fn original_digits(s: String) -> String {
        // 统计每个字母出现次数（仅需小写字母，用 26 长度数组即可，更省内存）
        let mut cnt = [0u32; 26];
        for b in s.bytes() {
            cnt[(b - b'a') as usize] += 1;
        }

        // ans[i] 表示数字 i 出现的次数
        let mut ans = [0u32; 10];

        // 处理一个数字：以其唯一标识字母的出现次数为该数字个数，
        // 并从字母计数中扣除该英文单词所消耗的字母
        let mut take = |cnt: &mut [u32; 26], digit: usize, key: u8, word: &str| {
            let k = cnt[(key - b'a') as usize];
            if k > 0 {
                ans[digit] = k;
                for b in word.bytes() {
                    cnt[(b - b'a') as usize] -= k;
                }
            }
        };

        take(&mut cnt, 0, b'z', "zero");
        take(&mut cnt, 2, b'w', "two");
        take(&mut cnt, 4, b'u', "four");
        take(&mut cnt, 6, b'x', "six");
        take(&mut cnt, 8, b'g', "eight");
        take(&mut cnt, 1, b'o', "one");
        take(&mut cnt, 3, b'r', "three");
        take(&mut cnt, 5, b'f', "five");
        take(&mut cnt, 7, b'v', "seven");
        take(&mut cnt, 9, b'e', "nine");

        // 按升序拼接结果
        let total: usize = ans.iter().map(|&x| x as usize).sum();
        let mut res = String::with_capacity(total);
        for (d, &c) in ans.iter().enumerate() {
            for _ in 0..c {
                res.push((b'0' + d as u8) as char);
            }
        }
        res
    }
}
```

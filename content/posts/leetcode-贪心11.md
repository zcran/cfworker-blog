---
title: "leetcode-贪心11"
date: 2026-10-03T12:55:34+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 最长回文串

给定一个包含大写字母和小写字母的字符串 s ，返回 通过这些字母构造成的 最长的 回文串 的长度。

在构造过程中，请注意 区分大小写 。比如 "Aa" 不能当做一个回文字符串。


```
impl Solution {
    pub fn longest_palindrome(s: String) -> i32 {
        // 用数组代替 HashMap，索引为 ASCII 码，大小写天然区分
        let mut cnt = [0u32; 128];
        for b in s.bytes() {
            cnt[b as usize] += 1;
        }

        // 每个字符的偶数部分都可对称放置；若有奇数字符，可额外放一个在中心
        let mut has_odd = false;
        let mut ans = 0;
        for c in cnt {
            ans += c & !1;      // 只取偶数部分
            has_odd |= c & 1 == 1;
        }

        ans as i32 + has_odd as i32
    }
}
```

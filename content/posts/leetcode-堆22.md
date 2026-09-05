---
title: "leetcode-堆22"
date: 2026-08-21T09:54:12+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 最长快乐字符串

如果字符串中不含有任何 'aaa'，'bbb' 或 'ccc' 这样的字符串作为子串，那么该字符串就是一个「快乐字符串」。

给你三个整数 a，b ，c，请你返回 任意一个 满足下列全部条件的字符串 s：

s 是一个尽可能长的快乐字符串。
s 中 最多 有a 个字母 'a'、b 个字母 'b'、c 个字母 'c' 。
s 中只含有 'a'、'b' 、'c' 三种字母。

如果不存在这样的字符串 s ，请返回一个空字符串 ""。



```
impl Solution {
    /// 贪心：每次选剩余最多的字符，若已连续两次则选次多的
    pub fn longest_diverse_string(mut a: i32, mut b: i32, mut c: i32) -> String {
        let mut res = String::new();
        let mut last = (' ', 0); // (上一个字符, 连续出现次数)

        loop {
            let mut arr = [(a, 'a'), (b, 'b'), (c, 'c')];
            arr.sort_unstable_by_key(|&(cnt, _)| std::cmp::Reverse(cnt));

            // 最多的字符已连续两次，则尝试次多的
            let i = if last.0 == arr[0].1 && last.1 == 2 { 1 } else { 0 };
            if arr[i].0 == 0 { break; }

            let ch = arr[i].1;
            res.push(ch);
            last = if ch == last.0 { (ch, last.1 + 1) } else { (ch, 1) };

            match ch {
                'a' => a -= 1,
                'b' => b -= 1,
                _ => c -= 1,
            }
        }

        res
    }
}
```

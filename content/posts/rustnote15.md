---
title: "Rust Note 15"
date: 2023-05-22T20:28:46+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 有效的字母异位词

给定两个字符串 s 和 t ，编写一个函数来判断 t 是否是 s 的字母异位词。

注意：若 s 和 t 中每个字符出现的次数都相同，则称 s 和 t 互为字母异位词。


### C 解法：
```
class Solution {
    public boolean isAnagram(String s, String t) {
        if(s.length() != t.length())
            return false;
        int[] alpha = new int[26];
        for(int i = 0; i< s.length(); i++) {
            alpha[s.charAt(i) - 'a'] ++;
            alpha[t.charAt(i) - 'a'] --;
        }
        for(int i=0;i<26;i++)
            if(alpha[i] != 0)
                return false;
        return true;
    }
}
```


### Rust 解法：
```
impl Solution {
    pub fn is_anagram(s: String, t: String) -> bool {
        if s.len() != t.len() {
            return false;
        }
        let mut hash = [0; 26];
        s.chars().for_each(|ch| hash[(ch as u8 -'a' as u8) as usize] += 1);
        t.chars().for_each(|ch| hash[(ch as u8 -'a' as u8) as usize] -= 1);
        !hash.iter().any(|&x| x != 0)
    }
}
```
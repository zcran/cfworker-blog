---
title: "Rust Note 14"
date: 2023-05-22T20:28:46+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 字符串中的第一个唯一字符

给定一个字符串 s ，找到 它的第一个不重复的字符，并返回它的索引 。如果不存在，则返回 -1 。


### C 解法：
```
class Solution {
public:
    int firstUniqChar(string s) {
        unordered_map<int, int> frequency;
        for (char ch: s) {
            ++frequency[ch];
        }
        for (int i = 0; i < s.size(); ++i) {
            if (frequency[s[i]] == 1) {
                return i;
            }
        }
        return -1;
    }
};
```


### Rust 解法1：
```
use std::collections::HashMap;
impl Solution {
    pub fn first_uniq_char(s: String) -> i32 {
        let mut map = HashMap::new();
        s.chars().for_each(|c| *map.entry(c).or_insert(0) += 1);
        for (i, c) in s.chars().enumerate() {
            if map[&c] == 1 {
                return i as i32;
            }
        }
        -1
    }
}
```

### Rust解法2:
```
impl Solution {
    pub fn first_uniq_char(s: String) -> i32 {
        let mut counts = [0; 26];
        let idx = |b: u8| (b - b'a') as usize;

        for b in s.bytes() {
            counts[idx(b)] += 1;
        }

        for (i, b) in s.bytes().enumerate() {
            if counts[idx(b)] == 1 {
                return i as i32;
            }
        }
        -1
    }
}
```

### Rust enumrate结构体
Struct std::iter::Enumerate
一个在迭代过程中产生当前计数和元素的迭代器。

该 struct 是通过 Iterator 上的 enumerate 方法创建的。 有关更多信息，请参见其文档。
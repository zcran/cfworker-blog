---
title: "leetcode-排序92"
date: 2026-10-05T00:35:06+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 变位词组

编写一种方法，对字符串数组进行排序，将所有变位词组合在一起。变位词是指字母相同，但排列不同的字符串。

注意：本题相对原题稍作修改


```
use std::collections::HashMap;

impl Solution {
    pub fn group_anagrams(strs: Vec<String>) -> Vec<Vec<String>> {
        // 以「字符排序后的结果」作为分组键，变位词会得到相同的键
        let mut groups: HashMap<Vec<u8>, Vec<String>> = HashMap::new();

        for s in strs {
            let mut key = s.clone().into_bytes();
            key.sort_unstable();
            groups.entry(key).or_default().push(s);
        }

        groups.into_values().collect()
    }
}
```

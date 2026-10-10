---
title: "leetcode-哈希表38"
date: 2026-10-09T20:07:57+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 保证文件名唯一

给你一个长度为 n 的字符串数组 names 。你将会在文件系统中创建 n 个文件夹：在第 i 分钟，新建名为 names[i] 的文件夹。

由于两个文件 不能 共享相同的文件名，因此如果新建文件夹使用的文件名已经被占用，系统会以 (k) 的形式为新文件夹的文件名添加后缀，其中 k 是能保证文件名唯一的 最小正整数 。

返回长度为 n 的字符串数组，其中 ans[i] 是创建第 i 个文件夹时系统分配给该文件夹的实际名称。


```
use std::collections::HashMap;

impl Solution {
    pub fn get_folder_names(names: Vec<String>) -> Vec<String> {
        // used: 已分配的实际名称集合，避免重复。
        // next: 基准名 -> 下一个待尝试的后缀编号，避免每次从 1 重新试探。
        let mut used: HashMap<String, ()> = HashMap::new();
        let mut next: HashMap<String, u32> = HashMap::new();

        names
            .into_iter()
            .map(|name| {
                let mut candidate = name.clone();
                // 若被占用，则按 next 递增尝试 name(k)，直到唯一。
                if used.contains_key(&candidate) {
                    let mut k = *next.get(&name).unwrap_or(&1);
                    loop {
                        candidate = format!("{}({})", name, k);
                        k += 1;
                        if !used.contains_key(&candidate) {
                            break;
                        }
                    }
                    next.insert(name.clone(), k);
                } else {
                    next.entry(name.clone()).or_insert(1);
                }
                used.insert(candidate.clone(), ());
                candidate
            })
            .collect()
    }
}
```

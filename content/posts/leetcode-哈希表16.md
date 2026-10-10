---
title: "leetcode-哈希表16"
date: 2026-10-09T20:07:56+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 在系统中查找重复文件

给你一个目录信息列表 paths ，包括目录路径，以及该目录中的所有文件及其内容，请你按路径返回文件系统中的所有重复文件。答案可按 任意顺序 返回。

一组重复的文件至少包括 两个 具有完全相同内容的文件。

输入 列表中的单个目录信息字符串的格式如下：

"root/d1/d2/.../dm f1.txt(f1_content) f2.txt(f2_content) ... fn.txt(fn_content)"
这意味着，在目录 root/d1/d2/.../dm 下，有 n 个文件 ( f1.txt, f2.txt ... fn.txt ) 的内容分别是 ( f1_content, f2_content ... fn_content ) 。注意：n >= 1 且 m >= 0 。如果 m = 0 ，则表示该目录是根目录。

输出 是由 重复文件路径组 构成的列表。其中每个组由所有具有相同内容文件的文件路径组成。文件路径是具有下列格式的字符串：

"directory_path/file_name.txt"


```
use std::collections::HashMap;

impl Solution {
    pub fn find_duplicate(paths: Vec<String>) -> Vec<Vec<String>> {
        // 以文件内容为键，聚合所有相同内容的文件路径。
        // 每条目录信息格式： "dir f1(content) f2(content) ..."
        let mut content_to_files: HashMap<&str, Vec<String>> = HashMap::new();

        for entry in &paths {
            let mut parts = entry.split(' ');
            let dir = parts.next().unwrap();
            for file in parts {
                // 拆出文件名与内容："name.txt(content)" -> ("name.txt", "content")
                let (name, content) = file.split_once('(').unwrap();
                let content = &content[..content.len() - 1]; // 去掉末尾 ')'
                content_to_files
                    .entry(content)
                    .or_default()
                    .push(format!("{dir}/{name}"));
            }
        }

        // 只保留出现次数大于 1 的内容对应的文件组。
        content_to_files
            .into_values()
            .filter(|files| files.len() > 1)
            .collect()
    }
}
```

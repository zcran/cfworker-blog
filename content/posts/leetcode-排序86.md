---
title: "leetcode-排序86"
date: 2026-10-05T00:35:05+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 寻找文件副本

设备中存有 n 个文件，文件 id 记于数组 documents。若文件 id 相同，则定义为该文件存在副本。请返回任一存在副本的文件 id。


```
impl Solution {
    pub fn find_repeat_document(mut documents: Vec<i32>) -> i32 {
        // 原地哈希：把值 v 放到下标 v 的位置上。
        // 若目标位置已经放着 v，说明 v 出现重复，直接返回。
        let mut i = 0;
        while i < documents.len() {
            let v = documents[i] as usize;
            if v == i {
                i += 1;
            } else if documents[v] == documents[i] {
                return documents[i];
            } else {
                documents.swap(i, v);
            }
        }

        -1
    }
}
```

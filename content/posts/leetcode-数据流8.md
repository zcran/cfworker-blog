---
title: "leetcode-数据流8"
date: 2026-04-06T20:57:54+08:00
tags: ["leetcode", "数据流"]
draft: false
---


## 设计有序流

有 n 个 (id, value) 对，其中 id 是 1 到 n 之间的一个整数，value 是一个字符串。不存在 id 相同的两个 (id, value) 对。

设计一个流，以 任意 顺序获取 n 个 (id, value) 对，并在多次调用时 按 id 递增的顺序 返回一些值。

实现 OrderedStream 类：

OrderedStream(int n) 构造一个能接收 n 个值的流，并将当前指针 ptr 设为 1 。
String[] insert(int id, String value) 向流中存储新的 (id, value) 对。存储后：
如果流存储有 id = ptr 的 (id, value) 对，则找出从 id = ptr 开始的 最长 id 连续递增序列 ，并 按顺序 返回与这些 id 关联的值的列表。然后，将 ptr 更新为最后那个  id + 1 。
否则，返回一个空列表。


```
struct OrderedStream {
    ptr: usize,
    data: Vec<Option<String>>,
}

impl OrderedStream {
    fn new(n: i32) -> Self {
        Self {
            ptr: 1,
            data: vec![None; n as usize + 2],
        }
    }

    fn insert(&mut self, id_key: i32, value: String) -> Vec<String> {
        self.data[id_key as usize] = Some(value);

        // 一行函数式实现
        let ans: Vec<String> = self.data[self.ptr..]
            .iter()
            .take_while(|opt| opt.is_some())
            .filter_map(|opt| opt.clone())
            .collect();

        self.ptr += ans.len();
        ans
    }
}
```

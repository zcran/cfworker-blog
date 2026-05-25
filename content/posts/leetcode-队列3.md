---
title: "leetcode-队列3"
date: 2026-05-23T09:21:37+08:00
tags: ["leetcode", "队列"]
draft: false
---


## 扁平化嵌套列表迭代器


给你一个嵌套的整数列表 nestedList 。每个元素要么是一个整数，要么是一个列表；该列表的元素也可能是整数或者是其他列表。请你实现一个迭代器将其扁平化，使之能够遍历这个列表中的所有整数。

实现扁平迭代器类 NestedIterator ：

· NestedIterator(List<NestedInteger> nestedList) 用嵌套列表 nestedList 初始化迭代器。
· int next() 返回嵌套列表的下一个整数。
· boolean hasNext() 如果仍然存在待迭代的整数，返回 true ；否则，返回 false 。

你的代码将会用下述伪代码检测：

```
initialize iterator with nestedList
res = []
while iterator.hasNext()
    append iterator.next() to the end of res
return res
```

如果 res 与预期的扁平化列表匹配，那么你的代码将会被判为正确。



```
pub struct NestedIterator {
    flattened: Vec<i32>,
    index: usize,
}

impl NestedIterator {
    pub fn new(nested_list: Vec<NestedInteger>) -> Self {
        fn flatten(list: Vec<NestedInteger>) -> Vec<i32> {
            list.into_iter().flat_map(|ni| match ni {
                NestedInteger::Int(x) => vec![x],
                NestedInteger::List(l) => flatten(l),
            }).collect()
        }
        Self {
            flattened: flatten(nested_list),
            index: 0,
        }
    }

    pub fn next(&mut self) -> i32 {
        let val = self.flattened[self.index];
        self.index += 1;
        val
    }

    pub fn has_next(&self) -> bool {
        self.index < self.flattened.len()
    }
}
```

---
title: "Leetcode 迭代器3"
date: 2023-12-19T15:49:00+08:00
tags: ["leetcode", "迭代器"]
draft: false
---

## RLE 迭代器

我们可以使用游程编码(即 RLE )来编码一个整数序列。在偶数长度 encoding ( 从 0 开始 )的游程编码数组中，对于所有偶数 i ，encoding[i] 告诉我们非负整数 encoding[i + 1] 在序列中重复的次数。

例如，序列 arr = [8,8,8,5,5] 可以被编码为 encoding =[3,8,2,5] 。encoding =[3,8,0,9,2,5] 和 encoding =[2,8,1,8,2,5] 也是 arr 有效的 RLE 。
给定一个游程长度的编码数组，设计一个迭代器来遍历它。

实现 RLEIterator 类:

RLEIterator(int[] encoded) 用编码后的数组初始化对象。
int next(int n) 以这种方式耗尽后 n 个元素并返回最后一个耗尽的元素。如果没有剩余的元素要耗尽，则返回 -1 。

```
struct RLEIterator {
    iterator: std::vec::IntoIter<i32>,
    remain: (i32, i32),
}


/** 
 * `&self` means the method takes an immutable reference.
 * If you need a mutable reference, change it to `&mut self` instead.
 */
impl RLEIterator {

    fn new(A: Vec<i32>) -> Self {
        Self {
            iterator: A.into_iter(),
            remain: (0, -1),
        }
    }

    fn next(&mut self, mut n: i32) -> i32 {
        while n > self.remain.0 {
            n -= self.remain.0;
            self.remain.0 = 0;
            match self.iterator.next() {
                Some(x) => self.remain = (x, self.iterator.next().unwrap()),
                None => return -1,
            }
        }

        self.remain.0 -= n;

        self.remain.1
    }
}
```
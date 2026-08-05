---
title: "leetcode-设计9"
date: 2026-07-14T10:41:24+08:00
tags: ["leetcode", "设计"]
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
/// 游程编码（RLE）迭代器，按需消耗编码序列
struct RLEIterator {
    /// 底层编码数组的迭代器，按 (count, value) 成对读取
    iter: std::vec::IntoIter<i32>,
    /// 当前块的剩余计数和值，(剩余次数, 值)
    /// 初始为 (0, -1) 表示无当前块
    current: (i32, i32),
}

impl RLEIterator {
    /// 使用 RLE 编码数组初始化迭代器
    ///
    /// 编码格式：[count1, value1, count2, value2, ...]
    /// 时间复杂度: O(1)，空间复杂度: O(1)
    pub fn new(encoded: Vec<i32>) -> Self {
        Self {
            iter: encoded.into_iter(),
            current: (0, -1),
        }
    }

    /// 消耗接下来的 n 个元素，返回最后一个被消耗的元素
    ///
    /// 如果没有足够的元素，返回 -1
    /// 时间复杂度: O(n / block_size) 平均，最坏 O(k)（k 为块数）
    pub fn next(&mut self, mut n: i32) -> i32 {
        // 持续消耗直到满足 n 个元素
        while n > self.current.0 {
            n -= self.current.0;
            self.current.0 = 0;

            // 加载下一个块 (count, value)
            match self.iter.next() {
                Some(count) => {
                    let value = self.iter.next().expect("Invalid RLE encoding");
                    self.current = (count, value);
                }
                None => return -1, // 没有更多元素
            }
        }

        // 当前块足够，减少剩余计数并返回值
        self.current.0 -= n;
        self.current.1
    }
}
```

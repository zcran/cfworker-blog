---
title: "leetcode-数据流9"
date: 2026-04-06T20:57:54+08:00
tags: ["leetcode", "数据流"]
draft: false
---


## 求出 MK 平均值

给你两个整数 m 和 k ，以及数据流形式的若干整数。你需要实现一个数据结构，计算这个数据流的 MK 平均值 。

MK 平均值 按照如下步骤计算：

1.如果数据流中的整数少于 m 个，MK 平均值 为 -1 ，否则将数据流中最后 m 个元素拷贝到一个独立的容器中。
2.从这个容器中删除最小的 k 个数和最大的 k 个数。
3.计算剩余元素的平均值，并 向下取整到最近的整数 。


请你实现 MKAverage 类：

MKAverage(int m, int k) 用一个空的数据流和两个整数 m 和 k 初始化 MKAverage 对象。
void addElement(int num) 往数据流中插入一个新的元素 num 。
int calculateMKAverage() 对当前的数据流计算并返回 MK 平均数 ，结果需 向下取整到最近的整数 。


```
use std::collections::{VecDeque, BTreeMap};

struct MKAverage {
    m: usize,
    k: usize,
    sum: i32,
    stream: VecDeque<i32>,
    sorted: BTreeMap<i32, i32>,
}

impl MKAverage {
    fn new(m: i32, k: i32) -> Self {
        Self {
            m: m as usize,
            k: k as usize,
            sum: 0,
            stream: VecDeque::new(),
            sorted: BTreeMap::new(),
        }
    }

    fn add_element(&mut self, num: i32) {
        self.stream.push_back(num);
        *self.sorted.entry(num).or_insert(0) += 1;
        self.sum += num;

        if self.stream.len() > self.m {
            let v = self.stream.pop_front().unwrap();
            *self.sorted.get_mut(&v).unwrap() -= 1;

            if self.sorted.get(&v).unwrap() == &0 {
                self.sorted.remove(&v);
            }

            self.sum -= v;
        }
    }

    fn calculate_mk_average(&self) -> i32 {
    if self.stream.len() < self.m {
        return -1;
    }

    let sum_top_n = |iter: &mut dyn Iterator<Item = (&i32, &i32)>, n: usize| {
        iter.flat_map(|(&v, &cnt)| std::iter::repeat(v).take(cnt as usize))
            .take(n)
            .sum::<i32>()
    };

    let min_sum = sum_top_n(&mut self.sorted.iter(), self.k);
    let max_sum = sum_top_n(&mut self.sorted.iter().rev(), self.k);

    (self.sum - min_sum - max_sum) / (self.m - 2 * self.k) as i32
}


}
```

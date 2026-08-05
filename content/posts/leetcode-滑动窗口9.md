---
title: "leetcode-滑动窗口9"
date: 2026-07-18T11:02:29+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 滑动窗口中位数

中位数是有序序列最中间的那个数。如果序列的长度是偶数，则没有最中间的数；此时中位数是最中间的两个数的平均数。

例如：

[2,3,4]，中位数是 3

[2,3]，中位数是 (2 + 3) / 2 = 2.5

给你一个数组 nums，有一个长度为 k 的窗口从最左端滑动到最右端。窗口中有 k 个数，每次窗口向右移动 1 位。你的任务是找出每次窗口移动后得到的新窗口中元素的中位数，并输出由它们组成的数组。


```
use std::collections::{BinaryHeap, HashMap};

/// 懒删除堆：支持删除任意元素，维护实际大小
struct LazyHeap {
    heap: BinaryHeap<i64>,
    to_remove: HashMap<i64, i32>,
    size: usize,
}

impl LazyHeap {
    fn new() -> Self {
        Self {
            heap: BinaryHeap::new(),
            to_remove: HashMap::new(),
            size: 0,
        }
    }

    fn len(&self) -> usize {
        self.size
    }

    fn push(&mut self, x: i64) {
        self.heap.push(x);
        self.size += 1;
    }

    fn remove(&mut self, x: i64) {
        *self.to_remove.entry(x).or_insert(0) += 1;
        self.size -= 1;
    }

    /// 清理堆顶所有待删除元素
    fn clean(&mut self) {
        while let Some(&top) = self.heap.peek() {
            if let Some(cnt) = self.to_remove.get_mut(&top) {
                if *cnt > 0 {
                    *cnt -= 1;
                    self.heap.pop();
                } else {
                    break;
                }
            } else {
                break;
            }
        }
    }

    fn top(&mut self) -> i64 {
        self.clean();
        *self.heap.peek().unwrap()
    }

    fn pop(&mut self) -> i64 {
        self.clean();
        self.size -= 1;
        self.heap.pop().unwrap()
    }
}

impl Solution {
    pub fn median_sliding_window(nums: Vec<i32>, k: i32) -> Vec<f64> {
        let k = k as usize;
        let n = nums.len();
        let mut ans = vec![0.0; n - k + 1];

        let mut small = LazyHeap::new(); // 最大堆（较小的一半）
        let mut large = LazyHeap::new(); // 最小堆（较大的一半，存储相反数）

        for (i, &num) in nums.iter().enumerate() {
            let x = num as i64;

            // 插入新元素，保持平衡
            if small.len() == large.len() {
                // 插入到 small，先经过 large 过滤
                large.push(-x);
                let val = -large.pop();
                small.push(val);
            } else {
                // 插入到 large，先经过 small 过滤
                small.push(x);
                let val = small.pop();
                large.push(-val);
            }

            // 窗口未满，继续
            if i + 1 < k {
                continue;
            }

            let left = i + 1 - k;

            // 计算中位数
            ans[left] = if k % 2 == 1 {
                small.top() as f64
            } else {
                (small.top() - large.top()) as f64 / 2.0
            };

            // 移除窗口左边界元素
            let out = nums[left] as i64;
            if out <= small.top() {
                small.remove(out);
                // 修正平衡：small 过小
                if small.len() < large.len() {
                    small.push(-large.pop());
                }
            } else {
                large.remove(-out);
                // 修正平衡：small 过大
                if small.len() > large.len() + 1 {
                    large.push(-small.pop());
                }
            }
        }

        ans
    }
}
```

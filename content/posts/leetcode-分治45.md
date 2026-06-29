---
title: "leetcode-分治45"
date: 2026-06-13T10:51:40+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 最小K个数

设计一个算法，找出数组中最小的k个数。以任意顺序返回这k个数均可。

```
use std::collections::BinaryHeap;  // 导入二叉堆（最大堆）数据结构

impl Solution {  // 为Solution结构体实现方法（LeetCode要求的入口）
    /// 找出数组中最小的k个数
    /// 使用最大堆维护当前找到的最小的k个元素
    pub fn smallest_k(arr: Vec<i32>, k: i32) -> Vec<i32> {
        // 边界情况：如果k为0，直接返回空向量
        if k == 0 {
            return vec![];
        }

        // 创建一个最大堆，初始容量为k（避免后续扩容）
        // BinaryHeap默认是最大堆，堆顶永远是最大的元素
        let mut heap = BinaryHeap::with_capacity(k as usize);

        // 遍历数组中的每个元素
        for n in arr {
            // 如果堆还没满（元素个数 < 容量k）
            if heap.len() < heap.capacity() {
                heap.push(n);  // 直接加入堆中
            } else {
                // 堆已满，需要判断当前元素是否应该替换堆顶（最大值）
                // pop()移除并返回堆顶元素（当前堆中的最大值）
                if let Some(top) = heap.pop() {
                    if n < top {
                        // 当前元素比堆顶小，说明当前元素应该进入最小k的集合
                        heap.push(n);  // 加入当前元素
                    } else {
                        // 当前元素比堆顶大，不需要加入，把堆顶放回去
                        heap.push(top);
                    }
                }
            }
        }

        // 将最大堆转换为Vec并返回
        // into()会消耗堆并返回内部存储的向量（元素顺序不保证）
        heap.into()
    }
}
```

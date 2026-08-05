---
title: "leetcode-设计30"
date: 2026-07-14T10:41:26+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 设计内存分配器

给你一个整数 n ，表示下标从 0 开始的内存数组的大小。所有内存单元开始都是空闲的。

请你设计一个具备以下功能的内存分配器：

1. 分配 一块大小为 size 的连续空闲内存单元并赋 id mID 。
2. 释放 给定 id mID 对应的所有内存单元。

注意：

· 多个块可以被分配到同一个 mID 。
· 你必须释放 mID 对应的所有内存单元，即便这些内存单元被分配在不同的块中。

实现 Allocator 类：

· Allocator(int n) 使用一个大小为 n 的内存数组初始化 Allocator 对象。
· int allocate(int size, int mID) 找出大小为 size 个连续空闲内存单元且位于  最左侧 的块，分配并赋 id mID 。返回块的第一个下标。如果不存在这样的块，返回 -1 。
· int freeMemory(int mID) 释放 id mID 对应的所有内存单元。返回释放的内存单元数目。


```
/// 内存分配器
///
/// 使用数组模拟内存，支持最左匹配分配和按 ID 释放
/// 时间复杂度: allocate O(n), free O(n)
/// 空间复杂度: O(n)
struct Allocator {
    memory: Vec<i32>, // 内存数组，0 表示空闲，>0 表示已分配
}

impl Allocator {
    /// 初始化内存大小为 n
    fn new(n: i32) -> Self {
        Allocator {
            memory: vec![0; n as usize],
        }
    }

    /// 分配 size 大小的连续空闲内存块（最左匹配）
    ///
    /// # 返回
    /// - 成功：分配的起始下标
    /// - 失败：-1
    fn allocate(&mut self, size: i32, m_id: i32) -> i32 {
        let size = size as usize;
        let mut start = 0;
        let mut free_len = 0;

        // 扫描内存，寻找最左的连续空闲块
        for i in 0..self.memory.len() {
            if self.memory[i] == 0 {
                free_len += 1;
                // 找到足够大的空闲块
                if free_len == size {
                    start = i - size + 1;
                    // 分配内存
                    for j in start..=i {
                        self.memory[j] = m_id;
                    }
                    return start as i32;
                }
            } else {
                // 遇到已占用块，重置计数
                free_len = 0;
            }
        }

        -1
    }

    /// 释放指定 mID 的所有内存块
    ///
    /// # 返回
    /// 释放的内存单元数量
    fn free_memory(&mut self, m_id: i32) -> i32 {
        let mut freed = 0;
        for slot in &mut self.memory {
            if *slot == m_id {
                *slot = 0;
                freed += 1;
            }
        }
        freed
    }
}
```
